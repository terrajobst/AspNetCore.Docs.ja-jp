---
title: ASP.NET Core のアプリケーション パーツ
author: rick-anderson
description: コントローラー、ビュー、Razor Pages などを ASP.NET Core のアプリケーション パーツと共有する
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4b4c8c554a7045a180b56cf9998ab1a8496cde1b
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207348"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>コントローラー、ビュー、Razor Pages などを ASP.NET Core のアプリケーション パーツと共有する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

"*アプリケーション パーツ*" は、アプリのリソースを抽象化したものです。 ASP.NET Core は、アプリケーション パーツを利用して、コントローラー、ビュー コンポーネント、タグ ヘルパー、Razor Pages、Razor コンパイル ソースなどを検出できます。 [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) はアプリケーション パーツです。 `AssemblyPart` はアセンブリ参照をカプセル化して、型とコンパイル参照を公開します。

"*機能プロバイダー*" は、アプリケーション パーツと連携し、ASP.NET Core アプリの機能を組み込みます。 アプリケーション パーツの主なユース ケースは、アセンブリから ASP.NET Core 機能を検出 (または読み込みを回避) するようにアプリを構成することです。 たとえば、複数のアプリで共通の機能を共有したいとします。 アプリケーション パーツを使用すると、コントローラー、ビュー、Razor Pages、Razor コンパイル ソース、タグ ヘルパーなどを含むアセンブリ (DLL) を複数のアプリと共有できます。 複数のプロジェクトでコードを複製するよりもアセンブリの共有をお勧めします。

ASP.NET Core アプリは <xref:System.Web.WebPages.ApplicationPart> から機能を読み込みます。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。

## <a name="load-aspnet-core-features"></a>ASP.NET Core 機能の読み込み

`ApplicationPart` および `AssemblyPart` クラスを使用し、ASP.NET Core 機能 (コントローラー、ビュー コンポーネントなど) を検出して読み込みます。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> は、使用可能なアプリケーション パーツと機能プロバイダーを追跡します。 `ApplicationPartManager` は `Startup.ConfigureServices` で構成されます。

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

次のコードは、`AssemblyPart` を使用して `ApplicationPartManager` を構成する別の方法を示しています。

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

上記 2 つのコード サンプルでは、アセンブリから `SharedController` が読み込まれます。 `SharedController` は、アプリケーションのプロジェクト内にありません。 [WebAppParts ソリューション](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)のサンプル ダウンロードを参照してください。

### <a name="include-views"></a>ビューを含める

アセンブリにビューを含めるには、次のようにします。

* 共有プロジェクト ファイルに次のマークアップを追加します。

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> を <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine> に追加します。

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>リソースの読み込みを防ぐ

アプリケーション パーツを使用して、特定のアセンブリまたは場所にあるソースの読み込みを "*回避*" することができます。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> コレクションのメンバーを追加または削除して、リソースを非表示にしたり使用可能にしたりします。 `ApplicationParts` コレクションでのエントリの順序は重要ではありません。 `ApplicationPartManager` を構成してから、コンテナーでサービスを構成するために使用します。 たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を構成します。 リソースを削除するには、`ApplicationParts` コレクションに対して `Remove` を呼び出します。

次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> を使用してアプリから `MyDependentLibrary` を削除します。[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager` には次のパーツが含まれています。

* アプリのアセンブリおよび依存アセンブリ。
* `Microsoft.AspNetCore.Mvc.TagHelpers`。
* `Microsoft.AspNetCore.Mvc.Razor`。

## <a name="application-feature-providers"></a>アプリケーション機能プロバイダー

アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。 次の ASP.NET Core 機能には組み込みの機能プロバイダーがあります。

* [コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [タグ ヘルパー](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [ビュー コンポーネント](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

機能プロバイダーは <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> から継承されます。ここで `T` は機能の種類です。 機能プロバイダーは、前述の機能の種類のいずれについても実装できます。 `ApplicationPartManager.FeatureProviders` の機能プロバイダーの順序が実行時の動作に影響することがあります。 後から追加されたプロバイダーは、前に追加されたプロバイダーによって行われたアクションに対応できます。

### <a name="generic-controller-feature"></a>汎用コント ローラーの機能

ASP.NET Core では[汎用コントローラー](/dotnet/csharp/programming-guide/generics/generic-classes)は無視されます。 汎用コントローラーには、型パラメーター (たとえば、`MyController<T>`) があります。 次の例では、指定された型リストの汎用コントローラー インスタンスが追加されます。

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

型は `EntityTypes.Types` に定義されます。

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

機能プロバイダーは `Startup` で追加されます。

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

ルーティングに使用される汎用コントローラー名の形式は、*Widget* ではなく、*GenericController`1[Widget]* です。 次の属性によって、コントローラーで使用されるジェネリック型に対応する名前が変更されます。

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` クラス:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

たとえば、`https://localhost:5001/Sprocket` の URL を要求すると次の応答が返されます。

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a>使用可能な機能の表示

アプリで使用できる機能を列挙するには、[依存関係の挿入](../../fundamentals/dependency-injection.md)によって `ApplicationPartManager` を要求します。

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)では、前のコードを使用してアプリの機能を表示します。

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```
