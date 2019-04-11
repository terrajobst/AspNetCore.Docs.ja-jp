---
title: Blazor 用のリンカーを構成する
author: guardrex
description: Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: host-and-deploy/razor-components-blazor/configure-linker
ms.openlocfilehash: 682bab92c2defa5ec941a1fa018e75e8a01819bc
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59070329"
---
# <a name="configure-the-linker-for-blazor"></a>Blazor 用のリンカーを構成する

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor では、各リリース モードのビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンク設定を実行して、アプリの出力アセンブリから不要な IL を削除します。

次の方法のいずれかを使って、アセンブリのリンクを制御します。

* [MSBuild プロパティ](#disable-linking-with-a-msbuild-property)を使ってリンクをグローバルに無効にする。
* [構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。

## <a name="disable-linking-with-a-msbuild-property"></a>MSBuild プロパティを使ってリンクを無効にする

リリース モードでは、アプリをビルドするときに既定でリンクが有効になります。これには公開が含まれます。 すべてのアセンブリに対してリンクを無効にするには、プロジェクト ファイルで MSBuild プロパティ `<BlazorLinkOnBuild>` を `false` に設定します。

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

詳細については、[IL リンカー:xml 記述子の構文](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)に関するページをご覧ください。