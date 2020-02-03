---
title: ASP.NET Core Blazor 用のリンカーを構成する
author: guardrex
description: Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 263b85a3213c1da233e4c96095faaf39d0a8e13f
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726766"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="a8a59-103">ASP.NET Core [!OP.NO-LOC(Blazor)] 用のリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="a8a59-103">Configure the Linker for ASP.NET Core [!OP.NO-LOC(Blazor)]</span></span>

<span data-ttu-id="a8a59-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8a59-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!OP.NO-LOC(Blazor)]<span data-ttu-id="a8a59-105"> では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL が削除されます。</span><span class="sxs-lookup"><span data-stu-id="a8a59-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="a8a59-106">次の方法のいずれかを使って、アセンブリのリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="a8a59-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="a8a59-107">[MSBuild プロパティ](#disable-linking-with-a-msbuild-property)を使ってリンクをグローバルに無効にする。</span><span class="sxs-lookup"><span data-stu-id="a8a59-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="a8a59-108">[構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。</span><span class="sxs-lookup"><span data-stu-id="a8a59-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="a8a59-109">MSBuild プロパティを使ってリンクを無効にする</span><span class="sxs-lookup"><span data-stu-id="a8a59-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="a8a59-110">アプリをビルドするときは既定でリンクが有効になり、これには発行が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a8a59-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="a8a59-111">すべてのアセンブリに対してリンクを無効にするには、プロジェクト ファイルで MSBuild プロパティ `BlazorLinkOnBuild` を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a8a59-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="a8a59-112">構成ファイルを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="a8a59-112">Control linking with a configuration file</span></span>

<span data-ttu-id="a8a59-113">XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="a8a59-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="a8a59-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="a8a59-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or [!OP.NO-LOC(Blazor)] packages must not be
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

<span data-ttu-id="a8a59-115">詳細については、[IL リンカー:xml 記述子の構文](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a8a59-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="a8a59-116">国際化用にリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="a8a59-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="a8a59-117">既定では、[!OP.NO-LOC(Blazor)] WebAssembly に対する [!OP.NO-LOC(Blazor)] のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="a8a59-117">By default, [!OP.NO-LOC(Blazor)]'s linker configuration for [!OP.NO-LOC(Blazor)] WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="a8a59-118">これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="a8a59-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="a8a59-119">保持される I18N アセンブリを制御するには、プロジェクト ファイルで MSBuild のプロパティ `<MonoLinkerI18NAssemblies>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="a8a59-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="a8a59-120">リージョンの値</span><span class="sxs-lookup"><span data-stu-id="a8a59-120">Region Value</span></span>     | <span data-ttu-id="a8a59-121">Mono のリージョン アセンブリ</span><span class="sxs-lookup"><span data-stu-id="a8a59-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="a8a59-122">すべてのアセンブリが含まれます</span><span class="sxs-lookup"><span data-stu-id="a8a59-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="a8a59-123">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="a8a59-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="a8a59-124">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="a8a59-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="a8a59-125">`none` (既定値)</span><span class="sxs-lookup"><span data-stu-id="a8a59-125">`none` (default)</span></span> | <span data-ttu-id="a8a59-126">None</span><span class="sxs-lookup"><span data-stu-id="a8a59-126">None</span></span>                    |
| `other`          | <span data-ttu-id="a8a59-127">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="a8a59-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="a8a59-128">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="a8a59-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="a8a59-129">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="a8a59-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="a8a59-130">複数の値を区切るにはコンマを使用します (例: `mideast,west`)。</span><span class="sxs-lookup"><span data-stu-id="a8a59-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="a8a59-131">詳しくは、「[I18N: Pnetlib 国際化フレームワーク ライブラリ (mono/mono GitHub リポジトリ)](https://github.com/mono/mono/tree/master/mcs/class/I18N)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a8a59-131">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
