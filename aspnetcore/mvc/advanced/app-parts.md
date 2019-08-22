---
title: ASP.NET Core のアプリケーション パーツ
author: ardalis
description: アプリのリソースで抽象化されたものである、アプリケーション パーツを使用して、アセンブリからの機能の検出または読み込みを回避する方法について説明します。
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4900ccf5589500db076f8cecd9da198c6a7ceea4
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886466"
---
<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

# <a name="application-parts-in-aspnet-core"></a>ASP.NET Core のアプリケーション パーツ

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

*アプリケーション パーツ*とは、コントローラー、ビュー コンポーネント、タグ ヘルパーなどの MVC 機能が検出される可能性のある、アプリケーションのリソースで抽象化されたものです。 アプリケーション パーツの一例として、AssemblyPart があります。これは、アセンブリ参照をカプセル化し、型とコンパイル参照を公開します。 *機能プロバイダー*はアプリケーション パーツを操作し、ASP.NET Core MVC アプリの機能を取り込みます。 アプリケーション パーツの主なユース ケースは、アセンブリから MVC 機能を検出 (または読み込みを回避) するようにアプリを構成できることです。

## <a name="introducing-application-parts"></a>アプリケーション パーツの概要

MVC アプリはその機能を[アプリケーション パーツ](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)から読み込みます。 たとえば、[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。 これらのクラスを使用して、コントローラー、ビュー コンポーネント、タグ ヘルパー、Razor コンパイル ソースなどの MBV 機能を検出して読み込むことができます。 [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) は、MVC アプリで使用できるアプリケーション パーツと機能プロバイダーの追跡を担当します。 MVC の構成時に `Startup` の `ApplicationPartManager` を操作できます。

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

既定では、MVC は依存関係ツリーを検索し、コントローラーを見つけます (他のアセンブリでも同様)。 任意のアセンブリを (たとえば、コンパイル時に参照されないプラグインから) 読み込む場合は、アプリケーション パーツを使用できます。

アプリケーション パーツを使用して、特定のアセンブリまたは場所でコントローラーを検索*しない* ようにすることができます。 `ApplicationPartManager` の `ApplicationParts` コレクションを変更して、アプリで使用できるパーツ (またはアセンブリ) を制御できます。 `ApplicationParts` コレクションでのエントリの順序は重要ではありません。 コンテナーでサービスを構成するために使用する前に、`ApplicationPartManager` を完全に構成することが重要です。 たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を完全に構成する必要があります。 そうしないと、メソッドの呼び出し後に追加されたアプリケーション パーツのコントローラーは影響を受けず (サービスとして登録されない)、アプリケーションの動作が不適切になる可能性があります。

使用しないコントローラーを含むアセンブリがある場合は、`ApplicationPartManager` からそれを削除します。

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

プロジェクトのアセンブリとそれに依存するアセンブリに加え、`ApplicationPartManager` には既定で `Microsoft.AspNetCore.Mvc.TagHelpers` と `Microsoft.AspNetCore.Mvc.Razor` のパーツが含まれます。

## <a name="application-feature-providers"></a>アプリケーション機能プロバイダー

アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。 次の MVC 機能には組み込みの機能プロバイダーがあります。

* [コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [メタデータ参照](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [タグ ヘルパー](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [ビュー コンポーネント](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

機能プロバイダーは `IApplicationFeatureProvider<T>` から継承されます。ここで `T` は機能の種類です。 上にリストされている MVC の機能のいずれかの種類に対して、独自の機能プロバイダーを実装することができます。 `ApplicationPartManager.FeatureProviders` コレクションでの機能プロバイダーの順序は重要な場合があります。前のプロバイダーによって行われたアクションに後のプロバイダーが反応する可能性があるためです。

### <a name="sample-generic-controller-feature"></a>サンプル:汎用コント ローラーの機能

既定では、ASP.NET Core MVC は汎用コントローラー (`SomeController<T>` など) を無視します。 このサンプルでは、既定のプロバイダーの後に実行されるコントローラー機能プロバイダーを使用して、指定された型のリスト (`EntityTypes.Types` で定義) に対して汎用コントローラー インスタンスを追加します。

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

エンティティ型:

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

機能プロバイダーは `Startup` で追加されます。

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

既定では、ルーティングに使用される汎用コントローラー名の形式は、*Widget* ではなく、*GenericController`1[Widget]* になります。 次の属性は、コントローラーで使用されるジェネリック型に対応する名前を変更するために使用します。

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` クラス:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

一致するルートが要求された場合の結果:

![サンプル アプリからの出力例は 'Hello from a generic Sprocket controller.' となっています](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>サンプル: 使用可能な機能の表示

[依存関係の挿入](../../fundamentals/dependency-injection.md)で `ApplicationPartManager` を要求し、それを使用して適切な機能のインスタンスを取り込むことで、アプリで使用可能な取り込まれた機能を反復処理することができます。

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

出力例:

![サンプル アプリからの出力例](app-parts/_static/available-features.png)
