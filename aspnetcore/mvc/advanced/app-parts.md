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
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="7a9a1-103">コントローラー、ビュー、Razor Pages などを ASP.NET Core のアプリケーション パーツと共有する</span><span class="sxs-lookup"><span data-stu-id="7a9a1-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="7a9a1-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7a9a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7a9a1-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7a9a1-106">"*アプリケーション パーツ*" は、アプリのリソースを抽象化したものです。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="7a9a1-107">ASP.NET Core は、アプリケーション パーツを利用して、コントローラー、ビュー コンポーネント、タグ ヘルパー、Razor Pages、Razor コンパイル ソースなどを検出できます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="7a9a1-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) はアプリケーション パーツです。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="7a9a1-109">`AssemblyPart` はアセンブリ参照をカプセル化して、型とコンパイル参照を公開します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="7a9a1-110">"*機能プロバイダー*" は、アプリケーション パーツと連携し、ASP.NET Core アプリの機能を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="7a9a1-111">アプリケーション パーツの主なユース ケースは、アセンブリから ASP.NET Core 機能を検出 (または読み込みを回避) するようにアプリを構成することです。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="7a9a1-112">たとえば、複数のアプリで共通の機能を共有したいとします。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="7a9a1-113">アプリケーション パーツを使用すると、コントローラー、ビュー、Razor Pages、Razor コンパイル ソース、タグ ヘルパーなどを含むアセンブリ (DLL) を複数のアプリと共有できます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="7a9a1-114">複数のプロジェクトでコードを複製するよりもアセンブリの共有をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="7a9a1-115">ASP.NET Core アプリは <xref:System.Web.WebPages.ApplicationPart> から機能を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="7a9a1-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="7a9a1-117">ASP.NET Core 機能の読み込み</span><span class="sxs-lookup"><span data-stu-id="7a9a1-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="7a9a1-118">`ApplicationPart` および `AssemblyPart` クラスを使用し、ASP.NET Core 機能 (コントローラー、ビュー コンポーネントなど) を検出して読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="7a9a1-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> は、使用可能なアプリケーション パーツと機能プロバイダーを追跡します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="7a9a1-120">`ApplicationPartManager` は `Startup.ConfigureServices` で構成されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="7a9a1-121">次のコードは、`AssemblyPart` を使用して `ApplicationPartManager` を構成する別の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="7a9a1-122">上記 2 つのコード サンプルでは、アセンブリから `SharedController` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="7a9a1-123">`SharedController` は、アプリケーションのプロジェクト内にありません。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="7a9a1-124">[WebAppParts ソリューション](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)のサンプル ダウンロードを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="7a9a1-125">ビューを含める</span><span class="sxs-lookup"><span data-stu-id="7a9a1-125">Include views</span></span>

<span data-ttu-id="7a9a1-126">アセンブリにビューを含めるには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-126">To include views in the assembly:</span></span>

* <span data-ttu-id="7a9a1-127">共有プロジェクト ファイルに次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="7a9a1-128"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> を <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine> に追加します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="7a9a1-129">リソースの読み込みを防ぐ</span><span class="sxs-lookup"><span data-stu-id="7a9a1-129">Prevent loading resources</span></span>

<span data-ttu-id="7a9a1-130">アプリケーション パーツを使用して、特定のアセンブリまたは場所にあるソースの読み込みを "*回避*" することができます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="7a9a1-131"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> コレクションのメンバーを追加または削除して、リソースを非表示にしたり使用可能にしたりします。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="7a9a1-132">`ApplicationParts` コレクションでのエントリの順序は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="7a9a1-133">`ApplicationPartManager` を構成してから、コンテナーでサービスを構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="7a9a1-134">たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を構成します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="7a9a1-135">リソースを削除するには、`ApplicationParts` コレクションに対して `Remove` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="7a9a1-136">次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> を使用してアプリから `MyDependentLibrary` を削除します。[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="7a9a1-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="7a9a1-137">`ApplicationPartManager` には次のパーツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="7a9a1-138">アプリのアセンブリおよび依存アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="7a9a1-139">`Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="7a9a1-140">`Microsoft.AspNetCore.Mvc.Razor`。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="7a9a1-141">アプリケーション機能プロバイダー</span><span class="sxs-lookup"><span data-stu-id="7a9a1-141">Application feature providers</span></span>

<span data-ttu-id="7a9a1-142">アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="7a9a1-143">次の ASP.NET Core 機能には組み込みの機能プロバイダーがあります。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="7a9a1-144">コントローラー</span><span class="sxs-lookup"><span data-stu-id="7a9a1-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="7a9a1-145">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="7a9a1-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="7a9a1-146">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7a9a1-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="7a9a1-147">機能プロバイダーは <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> から継承されます。ここで `T` は機能の種類です。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="7a9a1-148">機能プロバイダーは、前述の機能の種類のいずれについても実装できます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="7a9a1-149">`ApplicationPartManager.FeatureProviders` の機能プロバイダーの順序が実行時の動作に影響することがあります。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="7a9a1-150">後から追加されたプロバイダーは、前に追加されたプロバイダーによって行われたアクションに対応できます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="7a9a1-151">汎用コント ローラーの機能</span><span class="sxs-lookup"><span data-stu-id="7a9a1-151">Generic controller feature</span></span>

<span data-ttu-id="7a9a1-152">ASP.NET Core では[汎用コントローラー](/dotnet/csharp/programming-guide/generics/generic-classes)は無視されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="7a9a1-153">汎用コントローラーには、型パラメーター (たとえば、`MyController<T>`) があります。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="7a9a1-154">次の例では、指定された型リストの汎用コントローラー インスタンスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="7a9a1-155">型は `EntityTypes.Types` に定義されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="7a9a1-156">機能プロバイダーは `Startup` で追加されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="7a9a1-157">ルーティングに使用される汎用コントローラー名の形式は、*Widget* ではなく、*GenericController\`1[Widget]* です。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="7a9a1-158">次の属性によって、コントローラーで使用されるジェネリック型に対応する名前が変更されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="7a9a1-159">`GenericController` クラス:</span><span class="sxs-lookup"><span data-stu-id="7a9a1-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="7a9a1-160">たとえば、`https://localhost:5001/Sprocket` の URL を要求すると次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="7a9a1-161">使用可能な機能の表示</span><span class="sxs-lookup"><span data-stu-id="7a9a1-161">Display available features</span></span>

<span data-ttu-id="7a9a1-162">アプリで使用できる機能を列挙するには、[依存関係の挿入](../../fundamentals/dependency-injection.md)によって `ApplicationPartManager` を要求します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="7a9a1-163">[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)では、前のコードを使用してアプリの機能を表示します。</span><span class="sxs-lookup"><span data-stu-id="7a9a1-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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
