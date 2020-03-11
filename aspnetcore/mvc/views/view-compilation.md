---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: cd096bba5eb580c0a606699a2bf7c36442fb56f7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652496"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="587eb-103">ASP.NET Core での Razor ファイルのコンパイル</span><span class="sxs-lookup"><span data-stu-id="587eb-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="587eb-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="587eb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="587eb-105">関連する MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="587eb-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="587eb-106">ビルド時の Razor ファイルの公開はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="587eb-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="587eb-107">Razor ファイルはオプションで発行時にコンパイルして、アプリ &mdash; でプリコンパイル ツールを使用して展開できます。</span><span class="sxs-lookup"><span data-stu-id="587eb-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="587eb-108">関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="587eb-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="587eb-109">ビルド時の Razor ファイルの公開はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="587eb-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="587eb-110">Razor ファイルはオプションで発行時にコンパイルして、アプリ &mdash; でプリコンパイル ツールを使用して展開できます。</span><span class="sxs-lookup"><span data-stu-id="587eb-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="587eb-111">関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="587eb-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="587eb-112">Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="587eb-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="587eb-113">*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="587eb-113">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="587eb-114">実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。</span><span class="sxs-lookup"><span data-stu-id="587eb-114">Runtime compilation may be optionally enabled by configuring your application.</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="587eb-115">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="587eb-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="587eb-116">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="587eb-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="587eb-117">有効になっていると、実行時コンパイルはビルド時のコンパイルを補完し、編集された Razor ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="587eb-117">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="587eb-118">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="587eb-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="587eb-119">更新後の Razor ファイルの編集は、ビルド時にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="587eb-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="587eb-120">既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。</span><span class="sxs-lookup"><span data-stu-id="587eb-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="587eb-121">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="587eb-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="587eb-122">[Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="587eb-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="587eb-123">Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。</span><span class="sxs-lookup"><span data-stu-id="587eb-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="587eb-124">たとえば、 *.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。</span><span class="sxs-lookup"><span data-stu-id="587eb-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="587eb-125">プロジェクトのターゲットが .NET Framework である場合は、次のように [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="587eb-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="587eb-126">プロジェクトのターゲットが .NET Core である場合、変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="587eb-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="587eb-127">ASP.NET Core 2.x プロジェクト テンプレートは既定で、暗黙的に `MvcRazorCompileOnPublish` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="587eb-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="587eb-128">そのため、この要素は *.csproj* ファイルから安全に削除できます。</span><span class="sxs-lookup"><span data-stu-id="587eb-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="587eb-129">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="587eb-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="587eb-130">[Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="587eb-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="587eb-131">ASP.NET Core 2.0 で[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) を実行する場合、Razor ファイルのプリコンパイルを使用できません。</span><span class="sxs-lookup"><span data-stu-id="587eb-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="587eb-132">`MvcRazorCompileOnPublish` プロパティを `true` に設定して、[Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="587eb-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="587eb-133">次の *.csproj* サンプルでは、これらの設定が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="587eb-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="587eb-134">[.NET Core CLI publish コマンド](/dotnet/core/deploying/#framework-dependent-deployments-fdd)を使用して、[フレームワークに依存する展開](/dotnet/core/tools/dotnet-publish)用にアプリを準備します。</span><span class="sxs-lookup"><span data-stu-id="587eb-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="587eb-135">たとえば、プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="587eb-135">For example, execute the following command at the project root:</span></span>

```dotnetcli
dotnet publish -c Release
```

<span data-ttu-id="587eb-136">コンパイル済みの Razor ファイルを含む、 *\<project_name>.PrecompiledViews.dll* ファイルは、プリコンパイルが成功したときに生成されます。</span><span class="sxs-lookup"><span data-stu-id="587eb-136">A *\<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="587eb-137">たとえば、次のスクリーンショットは、*WebApplication1.PrecompiledViews.dll* 内の *Index.cshtml* のコンテンツを示しています。</span><span class="sxs-lookup"><span data-stu-id="587eb-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![DLL 内の Razor ビュー](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="587eb-139">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="587eb-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="587eb-140">ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。</span><span class="sxs-lookup"><span data-stu-id="587eb-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="587eb-141">ASP.NET Core MVC は、 *.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。</span><span class="sxs-lookup"><span data-stu-id="587eb-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="587eb-142">ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。</span><span class="sxs-lookup"><span data-stu-id="587eb-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="587eb-143"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> では、ディスク上で Razor ファイル (Razor ビューと Razor Pages) が変更された場合に、ファイルが再コンパイルおよび更新されるかどうかを判断する値が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="587eb-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="587eb-144">次の場合、規定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="587eb-144">The default value is `true` for:</span></span>

* <span data-ttu-id="587eb-145">アプリの互換性バージョンが <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 以前に設定されている場合</span><span class="sxs-lookup"><span data-stu-id="587eb-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="587eb-146">アプリの互換性バージョンが <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 以降に設定され、アプリが開発環境 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*> にある場合。</span><span class="sxs-lookup"><span data-stu-id="587eb-146">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="587eb-147">言い換えると、<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> が明示的に設定されていない限り、Razor ファイルは非開発環境では再コンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="587eb-147">In other words, Razor files wouldn't recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="587eb-148">アプリの互換バージョンの設定に関するガイダンスと例については、<xref:mvc/compatibility-version> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="587eb-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="587eb-149">すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="587eb-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="587eb-150">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="587eb-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="587eb-151">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="587eb-151">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="587eb-152">例 :</span><span class="sxs-lookup"><span data-stu-id="587eb-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="587eb-153">実行時コンパイルを条件付きで有効にする</span><span class="sxs-lookup"><span data-stu-id="587eb-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="587eb-154">実行時コンパイルをローカル開発でのみ有効にできます。</span><span class="sxs-lookup"><span data-stu-id="587eb-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="587eb-155">このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="587eb-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="587eb-156">コンパイル済みのビューを使用する。</span><span class="sxs-lookup"><span data-stu-id="587eb-156">Uses compiled views.</span></span>
* <span data-ttu-id="587eb-157">サイズが小さい。</span><span class="sxs-lookup"><span data-stu-id="587eb-157">Is smaller in size.</span></span>
* <span data-ttu-id="587eb-158">運用環境のファイル監視を有効にしない。</span><span class="sxs-lookup"><span data-stu-id="587eb-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="587eb-159">環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="587eb-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="587eb-160">アクティブな [ 値に基づいて、](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`Configuration` パッケージを条件付きで参照します。</span><span class="sxs-lookup"><span data-stu-id="587eb-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="587eb-161">プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="587eb-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="587eb-162">`AddRazorRuntimeCompilation` 変数が `ASPNETCORE_ENVIRONMENT` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="587eb-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="587eb-163">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="587eb-163">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
