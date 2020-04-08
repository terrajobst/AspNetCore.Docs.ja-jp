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
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218662"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>ASP.NET Core Blazor 用のリンカーを構成する

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL がトリミングされます。 デバッグ構成でビルドすると、リンカーは無効になります。 リンカーを有効にするには、アプリをリリース構成でビルドする必要があります。 Blazor WebAssembly アプリを配置する場合は、リリースでビルドすることをお勧めします。 

アプリをリンクするとサイズが最適化されますが、悪影響を及ぼす可能性があります。 リフレクションや関連する動的機能を使用するアプリは、トリミングされたときに中断する可能性があります。リンカーがこの動的な動作を認識せず、通常は実行時にリフレクションに必要な型を特定できないためです。 そのようなアプリをトリミングするには、コードと、アプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、リンカーに通知する必要があります。 

トリミングされたアプリが配置後に正しく動作するには、開発中にアプリのリリース ビルドを頻繁にテストすることが重要です。

Blazor アプリのリンクは、次の MSBuild 機能を使用して構成できます。

* [MSBuild プロパティ](#control-linking-with-an-msbuild-property)を使ってリンクをグローバルに構成する。
* [構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。

## <a name="control-linking-with-an-msbuild-property"></a>MSBuild プロパティを使ってリンクを制御する

リンクは、アプリが `Release` 構成でビルドされると有効になります。 これを変更するには、プロジェクト ファイルで `BlazorWebAssemblyEnableLinking` の MSBuild プロパティを構成します。

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>構成ファイルを使ってリンクを制御する

XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

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

詳細については、[Link xml ファイルの例 (mono/リンカー GitHub リポジトリ)](https://github.com/mono/linker#link-xml-file-examples) を参照してください。

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>XML リンカー構成ファイルをライブラリに追加する

特定のライブラリ用にリンカーを構成するには、XML リンカー構成ファイルを埋め込みリソースとしてライブラリに追加します。 埋め込みリソースの名前は、アセンブリと同じにする必要があります。

次の例では、*LinkerConfig.xml* ファイルが、ライブラリのアセンブリと同じ名前を持つ埋め込みリソースとして指定されています。

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>国際化用にリンカーを構成する

既定では、WebAssembly アプリに対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報が除去されます。 これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。

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
