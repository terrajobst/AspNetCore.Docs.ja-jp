---
title: ASP.NET Core のアプリケーション パーツ
author: rick-anderson
description: コントローラー、ビュー、Razor Pages などを ASP.NET Core のアプリケーション パーツと共有する
ms.author: riande
ms.date: 11/10/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 92c408adfad8af3dfd2572b625ae28ef24f64948
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905765"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="76d58-103">コントローラー、ビュー、Razor Pages などを ASP.NET Core のアプリケーション パーツと共有する</span><span class="sxs-lookup"><span data-stu-id="76d58-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="76d58-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76d58-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="76d58-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="76d58-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="76d58-106">"*アプリケーション パーツ*" は、アプリのリソースを抽象化したものです。</span><span class="sxs-lookup"><span data-stu-id="76d58-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="76d58-107">ASP.NET Core は、アプリケーション パーツを利用して、コントローラー、ビュー コンポーネント、タグ ヘルパー、Razor Pages、Razor コンパイル ソースなどを検出できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="76d58-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) はアプリケーション パーツです。</span><span class="sxs-lookup"><span data-stu-id="76d58-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="76d58-109">`AssemblyPart` はアセンブリ参照をカプセル化して、型とコンパイル参照を公開します。</span><span class="sxs-lookup"><span data-stu-id="76d58-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="76d58-110">"*機能プロバイダー*" は、アプリケーション パーツと連携し、ASP.NET Core アプリの機能を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="76d58-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="76d58-111">アプリケーション パーツの主なユース ケースは、アセンブリから ASP.NET Core 機能を検出 (または読み込みを回避) するようにアプリを構成することです。</span><span class="sxs-lookup"><span data-stu-id="76d58-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="76d58-112">たとえば、複数のアプリで共通の機能を共有したいとします。</span><span class="sxs-lookup"><span data-stu-id="76d58-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="76d58-113">アプリケーション パーツを使用すると、コントローラー、ビュー、Razor Pages、Razor コンパイル ソース、タグ ヘルパーなどを含むアセンブリ (DLL) を複数のアプリと共有できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="76d58-114">複数のプロジェクトでコードを複製するよりもアセンブリの共有をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="76d58-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="76d58-115">ASP.NET Core アプリは <xref:System.Web.WebPages.ApplicationPart> から機能を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="76d58-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="76d58-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。</span><span class="sxs-lookup"><span data-stu-id="76d58-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="76d58-117">ASP.NET Core 機能の読み込み</span><span class="sxs-lookup"><span data-stu-id="76d58-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="76d58-118">`ApplicationPart` および `AssemblyPart` クラスを使用し、ASP.NET Core 機能 (コントローラー、ビュー コンポーネントなど) を検出して読み込みます。</span><span class="sxs-lookup"><span data-stu-id="76d58-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="76d58-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> は、使用可能なアプリケーション パーツと機能プロバイダーを追跡します。</span><span class="sxs-lookup"><span data-stu-id="76d58-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="76d58-120">`ApplicationPartManager` は `Startup.ConfigureServices` で構成されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="76d58-121">次のコードは、`AssemblyPart` を使用して `ApplicationPartManager` を構成する別の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="76d58-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="76d58-122">上記 2 つのコード サンプルでは、アセンブリから `SharedController` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76d58-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="76d58-123">`SharedController` は、アプリケーションのプロジェクト内にありません。</span><span class="sxs-lookup"><span data-stu-id="76d58-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="76d58-124">[WebAppParts ソリューション](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)のサンプル ダウンロードを参照してください。</span><span class="sxs-lookup"><span data-stu-id="76d58-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="76d58-125">ビューを含める</span><span class="sxs-lookup"><span data-stu-id="76d58-125">Include views</span></span>

<span data-ttu-id="76d58-126">アセンブリにビューを含めるには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="76d58-126">To include views in the assembly:</span></span>

* <span data-ttu-id="76d58-127">共有プロジェクト ファイルに次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="76d58-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="76d58-128"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> を <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine> に追加します。</span><span class="sxs-lookup"><span data-stu-id="76d58-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="76d58-129">リソースの読み込みを防ぐ</span><span class="sxs-lookup"><span data-stu-id="76d58-129">Prevent loading resources</span></span>

<span data-ttu-id="76d58-130">アプリケーション パーツを使用して、特定のアセンブリまたは場所にあるソースの読み込みを "*回避*" することができます。</span><span class="sxs-lookup"><span data-stu-id="76d58-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="76d58-131"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> コレクションのメンバーを追加または削除して、リソースを非表示にしたり使用可能にしたりします。</span><span class="sxs-lookup"><span data-stu-id="76d58-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="76d58-132">`ApplicationParts` コレクションでのエントリの順序は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="76d58-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="76d58-133">`ApplicationPartManager` を構成してから、コンテナーでサービスを構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="76d58-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="76d58-134">たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を構成します。</span><span class="sxs-lookup"><span data-stu-id="76d58-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="76d58-135">リソースを削除するには、`ApplicationParts` コレクションに対して `Remove` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="76d58-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="76d58-136">次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> を使用してアプリから `MyDependentLibrary` を削除します。[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="76d58-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="76d58-137">`ApplicationPartManager` には次のパーツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="76d58-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="76d58-138">アプリのアセンブリおよび依存アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="76d58-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="76d58-139">`Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="76d58-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="76d58-140">`Microsoft.AspNetCore.Mvc.Razor`。</span><span class="sxs-lookup"><span data-stu-id="76d58-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="76d58-141">アプリケーション機能プロバイダー</span><span class="sxs-lookup"><span data-stu-id="76d58-141">Application feature providers</span></span>

<span data-ttu-id="76d58-142">アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="76d58-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="76d58-143">次の ASP.NET Core 機能には組み込みの機能プロバイダーがあります。</span><span class="sxs-lookup"><span data-stu-id="76d58-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="76d58-144">コントローラー</span><span class="sxs-lookup"><span data-stu-id="76d58-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="76d58-145">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="76d58-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="76d58-146">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="76d58-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="76d58-147">機能プロバイダーは <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> から継承されます。ここで `T` は機能の種類です。</span><span class="sxs-lookup"><span data-stu-id="76d58-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="76d58-148">機能プロバイダーは、前述の機能の種類のいずれについても実装できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="76d58-149">`ApplicationPartManager.FeatureProviders` の機能プロバイダーの順序が実行時の動作に影響することがあります。</span><span class="sxs-lookup"><span data-stu-id="76d58-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="76d58-150">後から追加されたプロバイダーは、前に追加されたプロバイダーによって行われたアクションに対応できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="76d58-151">汎用コント ローラーの機能</span><span class="sxs-lookup"><span data-stu-id="76d58-151">Generic controller feature</span></span>

<span data-ttu-id="76d58-152">ASP.NET Core では[汎用コントローラー](/dotnet/csharp/programming-guide/generics/generic-classes)は無視されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="76d58-153">汎用コントローラーには、型パラメーター (たとえば、`MyController<T>`) があります。</span><span class="sxs-lookup"><span data-stu-id="76d58-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="76d58-154">次の例では、指定された型リストの汎用コントローラー インスタンスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="76d58-155">型は `EntityTypes.Types` に定義されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="76d58-156">機能プロバイダーは `Startup` で追加されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="76d58-157">ルーティングに使用される汎用コントローラー名の形式は、*Widget* ではなく、*GenericController\`1[Widget]* です。</span><span class="sxs-lookup"><span data-stu-id="76d58-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="76d58-158">次の属性によって、コントローラーで使用されるジェネリック型に対応する名前が変更されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="76d58-159">`GenericController` クラス:</span><span class="sxs-lookup"><span data-stu-id="76d58-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="76d58-160">たとえば、`https://localhost:5001/Sprocket` の URL を要求すると次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="76d58-161">使用可能な機能の表示</span><span class="sxs-lookup"><span data-stu-id="76d58-161">Display available features</span></span>

<span data-ttu-id="76d58-162">アプリで使用できる機能を列挙するには、[依存関係の挿入](../../fundamentals/dependency-injection.md)によって `ApplicationPartManager` を要求します。</span><span class="sxs-lookup"><span data-stu-id="76d58-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="76d58-163">[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)では、前のコードを使用してアプリの機能を表示します。</span><span class="sxs-lookup"><span data-stu-id="76d58-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="76d58-164">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76d58-164">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="76d58-165">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="76d58-165">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="76d58-166">"*アプリケーション パーツ*" は、アプリのリソースを抽象化したものです。</span><span class="sxs-lookup"><span data-stu-id="76d58-166">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="76d58-167">ASP.NET Core は、アプリケーション パーツを利用して、コントローラー、ビュー コンポーネント、タグ ヘルパー、Razor Pages、Razor コンパイル ソースなどを検出できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-167">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="76d58-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) はアプリケーション パーツです。</span><span class="sxs-lookup"><span data-stu-id="76d58-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="76d58-169">`AssemblyPart` はアセンブリ参照をカプセル化して、型とコンパイル参照を公開します。</span><span class="sxs-lookup"><span data-stu-id="76d58-169">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="76d58-170">"*機能プロバイダー*" は、アプリケーション パーツと連携し、ASP.NET Core アプリの機能を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="76d58-170">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="76d58-171">アプリケーション パーツの主なユース ケースは、アセンブリから ASP.NET Core 機能を検出 (または読み込みを回避) するようにアプリを構成することです。</span><span class="sxs-lookup"><span data-stu-id="76d58-171">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="76d58-172">たとえば、複数のアプリで共通の機能を共有したいとします。</span><span class="sxs-lookup"><span data-stu-id="76d58-172">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="76d58-173">アプリケーション パーツを使用すると、コントローラー、ビュー、Razor Pages、Razor コンパイル ソース、タグ ヘルパーなどを含むアセンブリ (DLL) を複数のアプリと共有できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-173">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="76d58-174">複数のプロジェクトでコードを複製するよりもアセンブリの共有をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="76d58-174">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="76d58-175">ASP.NET Core アプリは <xref:System.Web.WebPages.ApplicationPart> から機能を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="76d58-175">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="76d58-176"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> クラスは、アセンブリでバックアップされるアプリケーション パーツを表します。</span><span class="sxs-lookup"><span data-stu-id="76d58-176">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="76d58-177">ASP.NET Core 機能の読み込み</span><span class="sxs-lookup"><span data-stu-id="76d58-177">Load ASP.NET Core features</span></span>

<span data-ttu-id="76d58-178">`ApplicationPart` および `AssemblyPart` クラスを使用し、ASP.NET Core 機能 (コントローラー、ビュー コンポーネントなど) を検出して読み込みます。</span><span class="sxs-lookup"><span data-stu-id="76d58-178">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="76d58-179"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> は、使用可能なアプリケーション パーツと機能プロバイダーを追跡します。</span><span class="sxs-lookup"><span data-stu-id="76d58-179">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="76d58-180">`ApplicationPartManager` は `Startup.ConfigureServices` で構成されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-180">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="76d58-181">次のコードは、`AssemblyPart` を使用して `ApplicationPartManager` を構成する別の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="76d58-181">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="76d58-182">上記 2 つのコード サンプルでは、アセンブリから `SharedController` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76d58-182">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="76d58-183">`SharedController` は、アプリケーションのプロジェクト内にありません。</span><span class="sxs-lookup"><span data-stu-id="76d58-183">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="76d58-184">[WebAppParts ソリューション](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)のサンプル ダウンロードを参照してください。</span><span class="sxs-lookup"><span data-stu-id="76d58-184">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="76d58-185">ビューを含める</span><span class="sxs-lookup"><span data-stu-id="76d58-185">Include views</span></span>

<span data-ttu-id="76d58-186">アセンブリにビューを含めるには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="76d58-186">To include views in the assembly:</span></span>

* <span data-ttu-id="76d58-187">共有プロジェクト ファイルに次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="76d58-187">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="76d58-188"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> を <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine> に追加します。</span><span class="sxs-lookup"><span data-stu-id="76d58-188">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="76d58-189">リソースの読み込みを防ぐ</span><span class="sxs-lookup"><span data-stu-id="76d58-189">Prevent loading resources</span></span>

<span data-ttu-id="76d58-190">アプリケーション パーツを使用して、特定のアセンブリまたは場所にあるソースの読み込みを "*回避*" することができます。</span><span class="sxs-lookup"><span data-stu-id="76d58-190">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="76d58-191"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> コレクションのメンバーを追加または削除して、リソースを非表示にしたり使用可能にしたりします。</span><span class="sxs-lookup"><span data-stu-id="76d58-191">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="76d58-192">`ApplicationParts` コレクションでのエントリの順序は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="76d58-192">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="76d58-193">`ApplicationPartManager` を構成してから、コンテナーでサービスを構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="76d58-193">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="76d58-194">たとえば、`AddControllersAsServices` を呼び出す前に `ApplicationPartManager` を構成します。</span><span class="sxs-lookup"><span data-stu-id="76d58-194">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="76d58-195">リソースを削除するには、`ApplicationParts` コレクションに対して `Remove` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="76d58-195">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="76d58-196">次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> を使用してアプリから `MyDependentLibrary` を削除します。[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="76d58-196">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="76d58-197">`ApplicationPartManager` には次のパーツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="76d58-197">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="76d58-198">アプリのアセンブリおよび依存アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="76d58-198">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="76d58-199">`Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="76d58-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="76d58-200">`Microsoft.AspNetCore.Mvc.Razor`。</span><span class="sxs-lookup"><span data-stu-id="76d58-200">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="76d58-201">アプリケーション機能プロバイダー</span><span class="sxs-lookup"><span data-stu-id="76d58-201">Application feature providers</span></span>

<span data-ttu-id="76d58-202">アプリケーション機能プロバイダーはアプリケーション パーツを調べ、これらのパーツの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="76d58-202">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="76d58-203">次の ASP.NET Core 機能には組み込みの機能プロバイダーがあります。</span><span class="sxs-lookup"><span data-stu-id="76d58-203">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="76d58-204">コントローラー</span><span class="sxs-lookup"><span data-stu-id="76d58-204">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="76d58-205">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="76d58-205">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="76d58-206">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="76d58-206">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="76d58-207">機能プロバイダーは <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> から継承されます。ここで `T` は機能の種類です。</span><span class="sxs-lookup"><span data-stu-id="76d58-207">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="76d58-208">機能プロバイダーは、前述の機能の種類のいずれについても実装できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-208">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="76d58-209">`ApplicationPartManager.FeatureProviders` の機能プロバイダーの順序が実行時の動作に影響することがあります。</span><span class="sxs-lookup"><span data-stu-id="76d58-209">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="76d58-210">後から追加されたプロバイダーは、前に追加されたプロバイダーによって行われたアクションに対応できます。</span><span class="sxs-lookup"><span data-stu-id="76d58-210">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="76d58-211">汎用コント ローラーの機能</span><span class="sxs-lookup"><span data-stu-id="76d58-211">Generic controller feature</span></span>

<span data-ttu-id="76d58-212">ASP.NET Core では[汎用コントローラー](/dotnet/csharp/programming-guide/generics/generic-classes)は無視されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-212">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="76d58-213">汎用コントローラーには、型パラメーター (たとえば、`MyController<T>`) があります。</span><span class="sxs-lookup"><span data-stu-id="76d58-213">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="76d58-214">次の例では、指定された型リストの汎用コントローラー インスタンスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-214">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="76d58-215">型は `EntityTypes.Types` に定義されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-215">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="76d58-216">機能プロバイダーは `Startup` で追加されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-216">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="76d58-217">ルーティングに使用される汎用コントローラー名の形式は、*Widget* ではなく、*GenericController\`1[Widget]* です。</span><span class="sxs-lookup"><span data-stu-id="76d58-217">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="76d58-218">次の属性によって、コントローラーで使用されるジェネリック型に対応する名前が変更されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-218">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="76d58-219">`GenericController` クラス:</span><span class="sxs-lookup"><span data-stu-id="76d58-219">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="76d58-220">たとえば、`https://localhost:5001/Sprocket` の URL を要求すると次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="76d58-220">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="76d58-221">使用可能な機能の表示</span><span class="sxs-lookup"><span data-stu-id="76d58-221">Display available features</span></span>

<span data-ttu-id="76d58-222">アプリで使用できる機能を列挙するには、[依存関係の挿入](../../fundamentals/dependency-injection.md)によって `ApplicationPartManager` を要求します。</span><span class="sxs-lookup"><span data-stu-id="76d58-222">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="76d58-223">[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)では、前のコードを使用してアプリの機能を表示します。</span><span class="sxs-lookup"><span data-stu-id="76d58-223">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

::: moniker-end
