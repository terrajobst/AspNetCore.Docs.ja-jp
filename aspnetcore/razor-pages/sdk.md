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
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core の Razor SDK

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>概要

[!INCLUDE[](~/includes/2.1-SDK.md)]が含まれています、 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK)。 Razor SDK とは、次のとおりです。

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
* ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。
* Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* ASP.NET Core MVC ベースのプロジェクトまたは[Blazor](xref:blazor/index)プロジェクトの[Razor](xref:mvc/views/razor)ファイルを含むプロジェクトをビルド、パッケージ、および発行するために必要です
* には、Razor (cshtml または razor) ファイルのコンパイルをカスタマイズするための定義済みのターゲット、プロパティ、および項目のセットが含まれています。
::: moniker-end


::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Razor SDK には、 `<Content>` `Include`属性が`**\*.cshtml`グロビングパターンに設定された要素が含まれています。 一致するファイルがパブリッシュされます。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Razor SDK には`<Content>` 、 `**\*.cshtml`属性`Include`がおよび`**\*.razor`グロビングパターンに設定された要素が含まれています。 一致するファイルがパブリッシュされます。

::: moniker-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Razor SDK を使用する

ほとんどの web アプリは、Razor の SDK を明示的に参照する必要はありません。

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
Razor SDK を使用して Razor ビューまたは Razor ページを含むクラス ライブラリを構築する方法は次のとおりです。

* `Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 通常、パッケージ参照を`Microsoft.AspNetCore.Mvc`ビルドし、Razor ページと Razor ビュー コンパイルに必要な追加の依存関係を受信するが必要です。 少なくとも、プロジェクトへのパッケージ参照を追加する必要があります。

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  `Microsoft.AspNetCore.Razor.Design`パッケージがプロジェクトに、Razor コンパイル タスクとターゲットを提供します。

  前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。 次のマークアップは、Razor ファイルの ASP.NET Core Razor ページ アプリをビルドする Razor SDK を使用するプロジェクト ファイルを示します。
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラスライブラリをビルドするには、Razor クラスライブラリプロジェクトテンプレートから始めることをお勧めします。 Blazor (razor) ファイルのビルドに使用される razor クラスライブラリには、少なくとも`Microsoft.AspNetCore.Components`パッケージへの参照が必要です。 Razor ビューまたはページ (cshtml ファイル) のビルドに使用される razor クラスライブラリは、 `netcoreapp3.0` `FrameworkReference`を対象としたものであり`Microsoft.AspNetCore.App`、を持つ必要があります。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`と`Microsoft.AspNetCore.Mvc.Razor.Extensions`でパッケージが含まれている、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。 ただし、バージョンのない`Microsoft.AspNetCore.App`パッケージ参照は、の最新バージョンが含まれていないアプリへのメタパッケージ`Microsoft.AspNetCore.Razor.Design`します。 プロジェクトは、一貫したバージョンを参照する必要があります`Microsoft.AspNetCore.Razor.Design`(または`Microsoft.AspNetCore.Mvc`) Razor の最新のビルド時の修正プログラムが含まれるようにします。 詳細については、[この GitHub の問題](https://github.com/aspnet/Razor/issues/2553)を参照してください。

::: moniker-end

### <a name="properties"></a>プロパティ

Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。

* `RazorCompileOnBuild` &ndash; ときに`true`、コンパイルし、プロジェクトのビルドの一部として Razor アセンブリを生成します。 既定値は `true` です。
* `RazorCompileOnPublish` &ndash; ときに`true`、コンパイルし、プロジェクトの発行の一部として Razor アセンブリを生成します。 既定値は `true` です。

プロパティと、次の表の項目は、入力と剃刀 SDK への出力の構成に使用されます。

::: moniker range=">= aspnetcore-3.0"
> [!WARNING]
ASP.NET Core 3.0 以降では、または`RazorCompileOnBuild` `RazorCompileOnPublish`が無効になっている場合、MVC ビューまたは Razor Pages は既定では提供されません。 アプリケーションでは、ランタイムコンパイルを`Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`使用して cshtml ファイルを処理する場合に、ランタイムコンパイルのサポートを追加するために、パッケージへの明示的な参照を追加する必要があります。
::: moniker-end


| 項目 | 説明 |
| ----- | ----------- |
| `RazorGenerate` | コード生成の入力である項目要素 (*cshtml*ファイル)。 |
| `RazorComponent` | コンポーネントコードの生成に入力する項目要素 (*razor*ファイル)。
| `RazorCompile` | Razor コンパイルターゲットへの入力である項目要素 ( *.cs*ファイル)。 Razor アセンブリにコンパイルする追加ファイルを指定する場合に使用します。`ItemGroup` |
| `RazorTargetAssemblyAttribute` | Razor アセンブリ用の属性をコード生成するために使用する項目要素です。 例:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 項目の要素が生成された Razor アセンブリに埋め込みリソースとして追加します。 |

| プロパティ | 説明 |
| -------- | ----------- |
| `RazorTargetName` | Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。 | 
| `RazorOutputPath` | Razor の出力ディレクトリです。 |
| `RazorCompileToolset` | Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。 有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 既定値は `true` です。 の`true`場合、web.config ファイル、 *json*ファイル、および*cshtml*ファイルがプロジェクトのコンテンツとして含まれます。 使用して参照されている場合`Microsoft.NET.Sdk.Web`、ファイル*wwwroot*し、構成ファイルも含まれています。 |
| `EnableDefaultRazorGenerateItems` | `true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。 |
| `GenerateRazorTargetAssemblyInfo` | ときに`true`、生成、 *.cs*ファイルで指定された属性を格納している`RazorAssemblyAttribute`コンパイル出力ファイルが含まれています。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。 |
| `CopyRazorGenerateFilesToPublishDirectory` | ときに`true`、コピー`RazorGenerate`項目 ( *.cshtml*) ファイルを発行ディレクトリ。 通常、Razor ファイルは必要ありません発行されたアプリの場合、それらのビルド時または発行時のコンパイルに参加します。 既定値は `false` です。 |
| `CopyRefAssembliesToPublishDirectory` | `true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。 通常、参照アセンブリは必要ありません発行されたアプリのビルド時または発行時に Razor コンパイルが発生した場合。 設定`true`発行されたアプリケーションがランタイムのコンパイルを必要とする場合。 などの値を設定`true`アプリを変更する場合 *.cshtml*実行時にファイルや埋め込みのビューを使用します。 既定値は `false` です。 |
| `IncludeRazorContentInPack` | ときに`true`、すべての Razor コンテンツ アイテム ( *.cshtml*ファイル)、生成された NuGet パッケージに含める対象としてマークされました。 既定値は `false` です。 |
| `EmbedRazorGenerateSources` | `true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。 既定値は `false` です。 |
| `UseRazorBuildServer` | `true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。 既定値は、`UseSharedCompilation` の値です。 |
| `GenerateMvcApplicationPartsAssemblyAttributes` | の`true`場合、SDK は、アプリケーションパーツの検出を実行するために、MVC によって実行時に使用される追加の属性を生成します。 |

プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。

### <a name="targets"></a>ターゲット

Razor SDK では、次の 2 つの主要なターゲットが定義されています。

* `RazorGenerate` &ndash; コード生成 *.cs*ファイルから`RazorGenerate`項目要素。 このターゲットの前後に実行する追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。
* `RazorCompile` &ndash; 生成されたコンパイル *.cs* Razor アセンブリへのファイルします。 このターゲットの前後に実行する追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。
* `RazorComponentGenerate`コードによって、項目`RazorComponent`要素の .cs ファイルが生成されます。 &ndash; このターゲットの前後に実行する追加のターゲットを指定するには、`RazorComponentGenerateDependsOn` プロパティを使用します。

### <a name="runtime-compilation-of-razor-views"></a>Razor ビューの実行時のコンパイル

* 既定では、Razor SDK は、実行時のコンパイルを実行するために必要な参照アセンブリを公開しません。 この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。 `CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。

* Web アプリの場合、アプリが対象とすることを確認して、 `Microsoft.NET.Sdk.Web` SDK。

## <a name="razor-language-version"></a>Razor 言語バージョン

`Microsoft.NET.Sdk.Web` SDK を対象とする場合、Razor 言語バージョンは、アプリのターゲットフレームワークのバージョンから推測されます。 SDK を`Microsoft.NET.Sdk.Razor`対象とするプロジェクトや、アプリが推定値とは異なる Razor 言語バージョンを必要とするまれな場合は、アプリのプロジェクトファイルで`<RazorLangVersion>`プロパティを設定することにより、バージョンを構成できます。

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor の言語バージョンは、ビルドされたランタイムのバージョンと密接に統合されています。 ランタイム向けに設計されていない言語バージョンをターゲットにすることはサポートされていないため、ビルドエラーが発生する可能性があります。

## <a name="additional-resources"></a>その他の技術情報

* [.NET Core の csproj 形式に追加されたもの](/dotnet/core/tools/csproj)
* [MSBuild プロジェクトの共通項目](/visualstudio/msbuild/common-msbuild-project-items)
