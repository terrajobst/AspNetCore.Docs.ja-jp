---
title: ASP.NET Core Blazor 用のリンカーを構成する
author: guardrex
description: Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b08ec26fb8d139223c57774600bc3cb19a56ac49
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083291"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="ce7fc-103">ASP.NET Core Blazor 用のリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="ce7fc-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ce7fc-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce7fc-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ce7fc-105">Blazor WebAssembly では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL がトリミングされます。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="ce7fc-106">デバッグ構成でビルドすると、リンカーは無効になります。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="ce7fc-107">リンカーを有効にするには、アプリをリリース構成でビルドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="ce7fc-108">Blazor WebAssembly アプリを配置する場合は、リリースでビルドすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="ce7fc-109">アプリをリンクするとサイズが最適化されますが、悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="ce7fc-110">リフレクションや関連する動的機能を使用するアプリは、トリミングされたときに中断する可能性があります。リンカーがこの動的な動作を認識せず、通常は実行時にリフレクションに必要な型を特定できないためです。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="ce7fc-111">そのようなアプリをトリミングするには、コードと、アプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、リンカーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="ce7fc-112">トリミングされたアプリが配置後に正しく動作するには、開発中にアプリのリリース ビルドを頻繁にテストすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="ce7fc-113">Blazor アプリのリンクは、次の MSBuild 機能を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="ce7fc-114">[MSBuild プロパティ](#control-linking-with-an-msbuild-property)を使ってリンクをグローバルに構成する。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="ce7fc-115">[構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="ce7fc-116">MSBuild プロパティを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="ce7fc-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="ce7fc-117">リンクは、アプリが `Release` 構成でビルドされると有効になります。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-117">Linking is enabled when an app is built in `Release` configuation.</span></span> <span data-ttu-id="ce7fc-118">これを変更するには、プロジェクト ファイルで `BlazorWebAssemblyEnableLinking` の MSBuild プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="ce7fc-119">構成ファイルを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="ce7fc-119">Control linking with a configuration file</span></span>

<span data-ttu-id="ce7fc-120">XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="ce7fc-121">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="ce7fc-121">*Linker.xml*:</span></span>

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

<span data-ttu-id="ce7fc-122">詳細については、[IL リンカー:xml 記述子の構文](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-122">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="ce7fc-123">国際化用にリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="ce7fc-123">Configure the linker for internationalization</span></span>

<span data-ttu-id="ce7fc-124">既定では、WebAssembly アプリに対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報が除去されます。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-124">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ce7fc-125">これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-125">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="ce7fc-126">保持される I18N アセンブリを制御するには、プロジェクト ファイルで MSBuild のプロパティ `<MonoLinkerI18NAssemblies>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-126">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="ce7fc-127">リージョンの値</span><span class="sxs-lookup"><span data-stu-id="ce7fc-127">Region Value</span></span>     | <span data-ttu-id="ce7fc-128">Mono のリージョン アセンブリ</span><span class="sxs-lookup"><span data-stu-id="ce7fc-128">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="ce7fc-129">すべてのアセンブリが含まれます</span><span class="sxs-lookup"><span data-stu-id="ce7fc-129">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="ce7fc-130">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="ce7fc-130">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="ce7fc-131">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="ce7fc-131">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="ce7fc-132">`none` (既定値)</span><span class="sxs-lookup"><span data-stu-id="ce7fc-132">`none` (default)</span></span> | <span data-ttu-id="ce7fc-133">None</span><span class="sxs-lookup"><span data-stu-id="ce7fc-133">None</span></span>                    |
| `other`          | <span data-ttu-id="ce7fc-134">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="ce7fc-134">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="ce7fc-135">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="ce7fc-135">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="ce7fc-136">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="ce7fc-136">*I18N.West.dll*</span></span>         |

<span data-ttu-id="ce7fc-137">複数の値を区切るにはコンマを使用します (例: `mideast,west`)。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-137">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="ce7fc-138">詳しくは、「[I18N: Pnetlib 国際化フレームワーク ライブラリ (mono/mono GitHub リポジトリ)](https://github.com/mono/mono/tree/master/mcs/class/I18N)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ce7fc-138">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
