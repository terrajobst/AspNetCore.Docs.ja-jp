---
title: ASP.NET Core 用の Microsoft.AspNetCore.App メタパッケージ
author: Rick-Anderson
description: Microsoft.AspNetCore.App 共有フレームワーク
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 3ce74bc7329a88ffc6f77baf6b8a311c02951318
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648956"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="32d97-103">ASP.NET Core 用の Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="32d97-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="32d97-104">ASP.NET Core 共有フレームワーク (`Microsoft.AspNetCore.App`) には、マイクロソフトによって開発およびサポートされるアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="32d97-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="32d97-105">`Microsoft.AspNetCore.App` は、[.NET Core 3.0 以降の SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) がインストールされるときにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="32d97-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="32d97-106">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、ランタイム コンポーネントと Targeting Pack を含みます。</span><span class="sxs-lookup"><span data-stu-id="32d97-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="32d97-107">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="32d97-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="32d97-108">`Microsoft.NET.Sdk.Web` SDK を対象とするプロジェクトは、`Microsoft.AspNetCore.App` フレームワークを暗黙に参照します。</span><span class="sxs-lookup"><span data-stu-id="32d97-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="32d97-109">これらのプロジェクトには、追加の参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="32d97-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="32d97-110">ASP.NET Core 共有フレームワーク:</span><span class="sxs-lookup"><span data-stu-id="32d97-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="32d97-111">サードパーティの依存関係は含まれません。</span><span class="sxs-lookup"><span data-stu-id="32d97-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="32d97-112">ASP.NET Core チームでサポートされるすべてのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="32d97-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="32d97-113">この機能では、.NET Core 2.x を対象とする ASP.NET Core 2.x が必要です。</span><span class="sxs-lookup"><span data-stu-id="32d97-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="32d97-114">ASP.NET Core 用の [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [メタパッケージ](/dotnet/core/packages#metapackages)は:</span><span class="sxs-lookup"><span data-stu-id="32d97-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="32d97-115">[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)、[Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)、[IX-Async](https://www.nuget.org/packages/System.Interactive.Async/) を除き、サードパーティの依存関係は含まれません。</span><span class="sxs-lookup"><span data-stu-id="32d97-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="32d97-116">含まれるサードパーティの依存関係は、主要なフレームワークの機能を保証するために必要なものです。</span><span class="sxs-lookup"><span data-stu-id="32d97-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="32d97-117">サードパーティの依存関係 (上で示したもの以外) を含むものを除き、ASP.NET Core チームによってサポートされているすべてのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="32d97-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="32d97-118">サードパーティの依存関係 (上で示したもの以外) を含むものを除き、Entity Framework Core チームによってサポートされているすべてのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="32d97-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="32d97-119">`Microsoft.AspNetCore.App` パッケージには、ASP.NET Core 2.x および Entity Framework Core 2.x のすべての機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="32d97-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="32d97-120">ASP.NET Core 2.x を対象とする既定のプロジェクト テンプレートは、このパッケージを使用します。</span><span class="sxs-lookup"><span data-stu-id="32d97-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="32d97-121">ASP.NET Core 2.x および Entity Framework Core 2.x を対象とするアプリケーションは、`Microsoft.AspNetCore.App` パッケージを使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="32d97-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="32d97-122">`Microsoft.AspNetCore.App` メタパッケージのバージョン番号は、最小の ASP.NET Core バージョンと Entity Framework Core バージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="32d97-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="32d97-123">`Microsoft.AspNetCore.App` メタパッケージを使うと、アプリを保護するバージョンの制限が提供されます。</span><span class="sxs-lookup"><span data-stu-id="32d97-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="32d97-124">`Microsoft.AspNetCore.App` 内のパッケージに対して (直接的ではなく) 推移的な依存関係を持つパッケージが含まれ、それらのバージョン番号が異なる場合、NuGet はエラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="32d97-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="32d97-125">アプリに追加される他のパッケージは、`Microsoft.AspNetCore.App` に含まれるパッケージのバージョンを変更できません。</span><span class="sxs-lookup"><span data-stu-id="32d97-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="32d97-126">バージョンの整合性により信頼性の高いエクスペリエンスが保証されます。</span><span class="sxs-lookup"><span data-stu-id="32d97-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="32d97-127">`Microsoft.AspNetCore.App` は、関連するビットのテストされていないバージョンの組み合わせが同じアプリ内で一緒に使われるのを防ぐように設計されました。</span><span class="sxs-lookup"><span data-stu-id="32d97-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="32d97-128">`Microsoft.AspNetCore.App` メタパッケージを使うアプリケーションでは、ASP.NET Core 共有フレームワークが自動的に利用されます。</span><span class="sxs-lookup"><span data-stu-id="32d97-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="32d97-129">`Microsoft.AspNetCore.App` メタパッケージを使用する場合、参照される ASP.NET Core NuGet パッケージの資産は、アプリケーションと共に配置**されません**&mdash;ASP.NET Core 共有フレームワークにはこれらの資産が含まれています。</span><span class="sxs-lookup"><span data-stu-id="32d97-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="32d97-130">共有フレームワーク内の資産は、アプリケーションの起動時間を向上させるためにプリコンパイルされています。</span><span class="sxs-lookup"><span data-stu-id="32d97-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="32d97-131">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="32d97-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="32d97-132">次のプロジェクト ファイルは ASP.NET Core の `Microsoft.AspNetCore.App` メタパッケージを参照し、一般的な ASP.NET Core 2.2 のテンプレートを表します。</span><span class="sxs-lookup"><span data-stu-id="32d97-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="32d97-133">上記のマークアップは、一般的な ASP.NET Core 2.x 以降のテンプレートを表します。</span><span class="sxs-lookup"><span data-stu-id="32d97-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="32d97-134">`Microsoft.AspNetCore.App` パッケージ参照のバージョン番号は指定されていません。</span><span class="sxs-lookup"><span data-stu-id="32d97-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="32d97-135">バージョンが指定されていない場合は、[暗黙的な](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md)バージョンが SDK によって指定されます (つまり `Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="32d97-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="32d97-136">SDK によって指定される暗黙的なバージョンを利用し、パッケージ参照ではバージョン番号を明示的に設定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="32d97-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="32d97-137">この方法に関して質問がある場合は、[Microsoft.AspNetCore.App の暗黙的なバージョンについてのディスカッション](https://github.com/dotnet/AspNetCore.Docs/issues/6430)で GitHub にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="32d97-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="32d97-138">ポータブル アプリの場合、暗黙的なバージョンは `major.minor.0` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="32d97-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="32d97-139">共有フレームワークのロールフォワード メカニズムは、インストールされている共有フレームワークの中で最新の互換性のあるバージョンを使ってアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="32d97-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="32d97-140">開発、テスト、運用で確実に同じバージョンが使われるようにするため、すべての環境に同じバージョンの共有フレームワークをインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="32d97-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="32d97-141">自己完結型アプリの場合は、暗黙的なバージョン番号は、インストールされている SDK にバンドルされている共有フレームワークの `major.minor.patch` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="32d97-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="32d97-142">`Microsoft.AspNetCore.App` 参照でバージョン番号を指定しても、共有フレームワークのバージョンが選択されることは保証**されません**。</span><span class="sxs-lookup"><span data-stu-id="32d97-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="32d97-143">たとえば、バージョン "2.2.1" が指定されているのに、インストールされているのは "2.2.3" であるものとします。</span><span class="sxs-lookup"><span data-stu-id="32d97-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="32d97-144">この場合、アプリは "2.2.3" を使います。</span><span class="sxs-lookup"><span data-stu-id="32d97-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="32d97-145">お勧めしませんが、ロールフォワード (パッチとマイナーの両方または一方) を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="32d97-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="32d97-146">.NET ホストのロールフォワードに関する詳細、およびその動作を構成する方法については、[.NET ホストのロールフォワード](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="32d97-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="32d97-147">`<Project Sdk` は、暗黙的バージョン `Microsoft.AspNetCore.App` を使用するように `Microsoft.NET.Sdk.Web` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="32d97-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="32d97-148">`<Project Sdk="Microsoft.NET.Sdk">` (末尾の `.Web` なし) を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="32d97-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="32d97-149">次の警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="32d97-149">The following warning is generated:</span></span>

  <span data-ttu-id="32d97-150">*Warning NU1604:Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound.Include a lower bound in the dependency version to ensure consistent restore results.* (警告 NU1604: プロジェクト依存関係 Microsoft.AspNetCore.App には下限が含まれていません。復元結果に一貫性が与えられるように、依存関係バージョンに下限を追加してください。)</span><span class="sxs-lookup"><span data-stu-id="32d97-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="32d97-151">これは、.NET Core 2.1 SDK に関する既知の問題です。</span><span class="sxs-lookup"><span data-stu-id="32d97-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="32d97-152">ASP.NET Core の更新</span><span class="sxs-lookup"><span data-stu-id="32d97-152">Update ASP.NET Core</span></span>

<span data-ttu-id="32d97-153">`Microsoft.AspNetCore.App` [メタパッケージ](/dotnet/core/packages#metapackages)は、NuGet から更新される従来型のパッケージではありません。</span><span class="sxs-lookup"><span data-stu-id="32d97-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="32d97-154">`Microsoft.NETCore.App` に似て、`Microsoft.AspNetCore.App` は共有ランタイムを表しており、NuGet の外部で処理される特殊なバージョン管理セマンティクスを持ちます。</span><span class="sxs-lookup"><span data-stu-id="32d97-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="32d97-155">詳しくは、「[パッケージ、メタパッケージ、フレームワーク](/dotnet/core/packages)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="32d97-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="32d97-156">ASP.NET Core を更新するには:</span><span class="sxs-lookup"><span data-stu-id="32d97-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="32d97-157">開発用コンピューターおよびビルド サーバーの場合:[.NET Core SDK](https://www.microsoft.com/net/download) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="32d97-157">On development machines and build servers: Download and install the [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="32d97-158">配置サーバーの場合:[.NET Core ランタイム](https://www.microsoft.com/net/download)をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="32d97-158">On deployment servers: Download and install the [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>

 <span data-ttu-id="32d97-159">アプリケーションは、アプリケーションの再起動時にインストールされている最新バージョンにロールフォワードされます。</span><span class="sxs-lookup"><span data-stu-id="32d97-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="32d97-160">プロジェクト ファイル内で `Microsoft.AspNetCore.App` バージョン番号を更新する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="32d97-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="32d97-161">詳細については、「[フレームワーク依存のアプリをロールフォワードする](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="32d97-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="32d97-162">アプリケーションで以前に `Microsoft.AspNetCore.All` を使っていた場合は、「[Microsoft.AspNetCore.All から Microsoft.AspNetCore.App への移行](xref:fundamentals/metapackage#migrate)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="32d97-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
