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
ms.openlocfilehash: cdcd62915b8f1bae26773ed91e55973527e158f6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160276"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor 用のリンカーを構成する

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL が削除されます。

次の方法のいずれかを使って、アセンブリのリンクを制御します。

* [MSBuild プロパティ](#disable-linking-with-a-msbuild-property)を使ってリンクをグローバルに無効にする。
* [構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。

## <a name="disable-linking-with-a-msbuild-property"></a>MSBuild プロパティを使ってリンクを無効にする

アプリをビルドするときは既定でリンクが有効になり、これには発行が含まれます。 すべてのアセンブリに対してリンクを無効にするには、プロジェクト ファイルで MSBuild プロパティ `BlazorLinkOnBuild` を `false` に設定します。

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>構成ファイルを使ってリンクを制御する

XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

*Linker.xml*:

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

詳細については、[IL リンカー:xml 記述子の構文](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)に関するページをご覧ください。

### <a name="configure-the-linker-for-internationalization"></a>国際化用にリンカーを構成する

既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。 これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。

保持される I18N アセンブリを制御するには、プロジェクト ファイルで MSBuild のプロパティ `<MonoLinkerI18NAssemblies>` を設定します。

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| リージョンの値     | Mono のリージョン アセンブリ    |
| ---------------- | ----------------------- |
| `all`            | すべてのアセンブリが含まれます |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none` (既定値) | None                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

複数の値を区切るにはコンマを使用します (例: `mideast,west`)。

詳しくは、「[I18N: Pnetlib 国際化フレームワーク ライブラリ (mono/mono GitHub リポジトリ)](https://github.com/mono/mono/tree/master/mcs/class/I18N)」をご覧ください。
