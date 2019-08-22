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

# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="6b456-103">ASP.NET Core のアプリケーション パーツ</span><span class="sxs-lookup"><span data-stu-id="6b456-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="6b456-104">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6b456-104">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6b456-105">*アプリケーション パーツ*とは、コントローラー、ビュー コンポーネント、タグ ヘルパーなどの MVC 機能が検出される可能性のある、アプリケーションのリソースで抽象化されたものです。</span><span class="sxs-lookup"><span data-stu-id="6b456-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="6b456-106">アプリケーション パーツの一例として、AssemblyPart があります。これは、アセンブリ参照をカプセル化し、型とコンパイル参照を公開します。</span><span class="sxs-lookup"><span data-stu-id="6b456-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="6b456-107">*機能プロバイダー*はアプリケーション パーツを操作し、ASP.NET Core MVC アプリの機能を取り込みます。</span><span class="sxs-lookup"><span data-stu-id="6b456-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="6b456-108">アプリケーション パーツの主なユース ケースは、アセンブリから MVC 機能を検出 (または読み込みを回避) するようにアプリを構成できることです。</span><span class="sxs-lookup"><span data-stu-id="6b456-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="6b456-109">アプリケーション パーツの概要</span><span class="sxs-lookup"><span data-stu-id="6b456-109">Introducing Application Parts</span></span>

<span data-ttu-id="6b456-110">MVC アプリはその機能を[アプリケーション パーツ](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)から読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6b456-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="6b456-111">たとえば、[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。</span><span class="sxs-lookup"><span data-stu-id="6b456-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="6b456-112">これらのクラスを使用して、コントローラー、ビュー コンポーネント、タグ ヘルパー、Razor コンパイル ソースなどの MBV 機能を検出して読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="6b456-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="6b456-113">[ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) は、MVC アプリで使用できるアプリケーション パーツと機能プロバイダーの追跡を担当します。</span><span class="sxs-lookup"><span data-stu-id="6b456-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="6b456-114">MVC の構成時に `Startup` の `ApplicationPartManager` を操作できます。</span><span class="sxs-lookup"><span data-stu-id="6b456-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

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

<span data-ttu-id="6b456-115">既定では、MVC は依存関係ツリーを検索し、コントローラーを見つけます (他のアセンブリでも同様)。</span><span class="sxs-lookup"><span data-stu-id="6b456-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="6b456-116">任意のアセンブリを (たとえば、コンパイル時に参照されないプラグインから) 読み込む場合は、アプリケーション パーツを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b456-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="6b456-117">アプリケーション パーツを使用して、特定のアセンブリまたは場所でコントローラーを検索*しない* ようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b456-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="6b456-118">`ApplicationPartManager` の `ApplicationParts` コレクションを変更して、アプリで使用できるパーツ (またはアセンブリ) を制御できます。</span><span class="sxs-lookup"><span data-stu-id="6b456-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="6b456-119">`ApplicationParts` コレクションでのエントリの順序は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="6b456-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="6b456-120">コンテナーでサービスを構成するために使用する前に、`ApplicationPartManager` を完全に構成することが重要です。</span><span class="sxs-lookup"><span data-stu-id="6b456-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="6b456-121">たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を完全に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b456-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="6b456-122">そうしないと、メソッドの呼び出し後に追加されたアプリケーション パーツのコントローラーは影響を受けず (サービスとして登録されない)、アプリケーションの動作が不適切になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6b456-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="6b456-123">使用しないコントローラーを含むアセンブリがある場合は、`ApplicationPartManager` からそれを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b456-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

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

<span data-ttu-id="6b456-124">プロジェクトのアセンブリとそれに依存するアセンブリに加え、`ApplicationPartManager` には既定で `Microsoft.AspNetCore.Mvc.TagHelpers` と `Microsoft.AspNetCore.Mvc.Razor` のパーツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b456-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="6b456-125">アプリケーション機能プロバイダー</span><span class="sxs-lookup"><span data-stu-id="6b456-125">Application Feature Providers</span></span>

<span data-ttu-id="6b456-126">アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="6b456-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="6b456-127">次の MVC 機能には組み込みの機能プロバイダーがあります。</span><span class="sxs-lookup"><span data-stu-id="6b456-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="6b456-128">コントローラー</span><span class="sxs-lookup"><span data-stu-id="6b456-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="6b456-129">メタデータ参照</span><span class="sxs-lookup"><span data-stu-id="6b456-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="6b456-130">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6b456-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="6b456-131">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6b456-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="6b456-132">機能プロバイダーは `IApplicationFeatureProvider<T>` から継承されます。ここで `T` は機能の種類です。</span><span class="sxs-lookup"><span data-stu-id="6b456-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="6b456-133">上にリストされている MVC の機能のいずれかの種類に対して、独自の機能プロバイダーを実装することができます。</span><span class="sxs-lookup"><span data-stu-id="6b456-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="6b456-134">`ApplicationPartManager.FeatureProviders` コレクションでの機能プロバイダーの順序は重要な場合があります。前のプロバイダーによって行われたアクションに後のプロバイダーが反応する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="6b456-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="6b456-135">サンプル:汎用コント ローラーの機能</span><span class="sxs-lookup"><span data-stu-id="6b456-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="6b456-136">既定では、ASP.NET Core MVC は汎用コントローラー (`SomeController<T>` など) を無視します。</span><span class="sxs-lookup"><span data-stu-id="6b456-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="6b456-137">このサンプルでは、既定のプロバイダーの後に実行されるコントローラー機能プロバイダーを使用して、指定された型のリスト (`EntityTypes.Types` で定義) に対して汎用コントローラー インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b456-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="6b456-138">エンティティ型:</span><span class="sxs-lookup"><span data-stu-id="6b456-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="6b456-139">機能プロバイダーは `Startup` で追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b456-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="6b456-140">既定では、ルーティングに使用される汎用コントローラー名の形式は、*Widget* ではなく、*GenericController\`1[Widget]* になります。</span><span class="sxs-lookup"><span data-stu-id="6b456-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="6b456-141">次の属性は、コントローラーで使用されるジェネリック型に対応する名前を変更するために使用します。</span><span class="sxs-lookup"><span data-stu-id="6b456-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="6b456-142">`GenericController` クラス:</span><span class="sxs-lookup"><span data-stu-id="6b456-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="6b456-143">一致するルートが要求された場合の結果:</span><span class="sxs-lookup"><span data-stu-id="6b456-143">The result, when a matching route is requested:</span></span>

![サンプル アプリからの出力例は 'Hello from a generic Sprocket controller.' となっています](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="6b456-145">サンプル: 使用可能な機能の表示</span><span class="sxs-lookup"><span data-stu-id="6b456-145">Sample: Display available features</span></span>

<span data-ttu-id="6b456-146">[依存関係の挿入](../../fundamentals/dependency-injection.md)で `ApplicationPartManager` を要求し、それを使用して適切な機能のインスタンスを取り込むことで、アプリで使用可能な取り込まれた機能を反復処理することができます。</span><span class="sxs-lookup"><span data-stu-id="6b456-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="6b456-147">出力例:</span><span class="sxs-lookup"><span data-stu-id="6b456-147">Example output:</span></span>

![サンプル アプリからの出力例](app-parts/_static/available-features.png)
