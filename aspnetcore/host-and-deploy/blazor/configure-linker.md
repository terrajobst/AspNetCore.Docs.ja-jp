---
title: Blazor 用のリンカーを構成する
author: guardrex
description: Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 01e18498a16e86392755b02b92ffda929669cb7d
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614687"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="2c31e-103">Blazor 用のリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="2c31e-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="2c31e-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2c31e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="2c31e-105">Blazor では、各リリース モードのビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンク設定を実行して、アプリの出力アセンブリから不要な IL を削除します。</span><span class="sxs-lookup"><span data-stu-id="2c31e-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during each Release mode build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="2c31e-106">次の方法のいずれかを使って、アセンブリのリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="2c31e-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="2c31e-107">[MSBuild プロパティ](#disable-linking-with-a-msbuild-property)を使ってリンクをグローバルに無効にする。</span><span class="sxs-lookup"><span data-stu-id="2c31e-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="2c31e-108">[構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。</span><span class="sxs-lookup"><span data-stu-id="2c31e-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="2c31e-109">MSBuild プロパティを使ってリンクを無効にする</span><span class="sxs-lookup"><span data-stu-id="2c31e-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="2c31e-110">リリース モードでは、アプリをビルドするときに既定でリンクが有効になります。これには公開が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c31e-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="2c31e-111">すべてのアセンブリに対してリンクを無効にするには、プロジェクト ファイルで MSBuild プロパティ `<BlazorLinkOnBuild>` を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2c31e-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="2c31e-112">構成ファイルを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="2c31e-112">Control linking with a configuration file</span></span>

<span data-ttu-id="2c31e-113">XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="2c31e-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="2c31e-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="2c31e-114">*Linker.xml*:</span></span>

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
      Fixes: https://github.com/aspnet/Blazor/issues/239
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

<span data-ttu-id="2c31e-115">詳細については、[IL リンカー:xml 記述子の構文](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c31e-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>