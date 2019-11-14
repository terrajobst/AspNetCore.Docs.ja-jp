---
title: ASP.NET Core でのファイル プロバイダー
author: guardrex
description: ASP.NET Core がファイル プロバイダーを使用してファイル システムへのアクセスを抽象化する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 531f7acd7a704a74e6142d201f613f05288deecb
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896853"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="50df7-103">ASP.NET Core でのファイル プロバイダー</span><span class="sxs-lookup"><span data-stu-id="50df7-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="50df7-104">作成者: [Steve Smith](https://ardalis.com/)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50df7-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50df7-105">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="50df7-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="50df7-106">ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="50df7-107">`IWebHostEnvironment` では、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)と [Web ルート](xref:fundamentals/index#web-root)が `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="50df7-108">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="50df7-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="50df7-109">[Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="50df7-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="50df7-110">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="50df7-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="50df7-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="50df7-112">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="50df7-112">File Provider interfaces</span></span>

<span data-ttu-id="50df7-113">プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。</span><span class="sxs-lookup"><span data-stu-id="50df7-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="50df7-114">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="50df7-115">ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="50df7-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="50df7-116">ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="50df7-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="50df7-117">変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。</span><span class="sxs-lookup"><span data-stu-id="50df7-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="50df7-118">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="50df7-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="50df7-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="50df7-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付</span><span class="sxs-lookup"><span data-stu-id="50df7-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="50df7-121">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) メソッドを使用してファイルから読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="50df7-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="50df7-122">サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="50df7-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="50df7-123">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="50df7-123">File Provider implementations</span></span>

<span data-ttu-id="50df7-124">利用できる `IFileProvider` の実装は 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="50df7-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="50df7-125">実装</span><span class="sxs-lookup"><span data-stu-id="50df7-125">Implementation</span></span> | <span data-ttu-id="50df7-126">説明</span><span class="sxs-lookup"><span data-stu-id="50df7-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="50df7-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="50df7-128">システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="50df7-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="50df7-130">アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="50df7-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="50df7-132">コンポジット プロパイダーは、その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="50df7-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-133">PhysicalFileProvider</span></span>

<span data-ttu-id="50df7-134"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="50df7-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="50df7-135">`PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="50df7-136">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="50df7-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="50df7-137">`PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。</span><span class="sxs-lookup"><span data-stu-id="50df7-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="50df7-138">このプロバイダーを直接インスタンス化するときは、ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="50df7-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="50df7-139">次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="50df7-140">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="50df7-140">Types in the preceding example:</span></span>

* <span data-ttu-id="50df7-141">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="50df7-142">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="50df7-143">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="50df7-144">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="50df7-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="50df7-145">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="50df7-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="50df7-146">サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) を使用してプロバイダーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="50df7-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="50df7-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="50df7-148"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="50df7-149">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="50df7-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="50df7-150">[Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) パッケージに対するプロジェクトに、パッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="50df7-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="50df7-151">埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="50df7-152">[\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="50df7-153">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="50df7-154">サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="50df7-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="50df7-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="50df7-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="50df7-156">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="50df7-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="50df7-157">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-157">Specify a relative file path.</span></span>
* <span data-ttu-id="50df7-158">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="50df7-159">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="50df7-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="50df7-160">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="50df7-160">Overload</span></span> | <span data-ttu-id="50df7-161">説明</span><span class="sxs-lookup"><span data-stu-id="50df7-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="50df7-162">必要に応じて相対パスのパラメーター `root` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="50df7-163">`root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="50df7-164">必要に応じて、相対パス パラメーター `root` および日付パラメーター `lastModified` (<xref:System.DateTimeOffset>) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="50df7-165">`lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="50df7-166">必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="50df7-167">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="50df7-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="50df7-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-168">CompositeFileProvider</span></span>

<span data-ttu-id="50df7-169"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="50df7-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="50df7-170">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="50df7-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="50df7-171">サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="50df7-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="50df7-172">変更の監視</span><span class="sxs-lookup"><span data-stu-id="50df7-172">Watch for changes</span></span>

<span data-ttu-id="50df7-173">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="50df7-174">`Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="50df7-175">`Watch` では <xref:Microsoft.Extensions.Primitives.IChangeToken> が返されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="50df7-176">変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-176">The change token exposes:</span></span>

* <span data-ttu-id="50df7-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="50df7-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="50df7-179">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="50df7-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="50df7-180">定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="50df7-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="50df7-181">サンプル アプリでは、*WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="50df7-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="50df7-182">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="50df7-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="50df7-183">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="50df7-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="50df7-184">glob パターン</span><span class="sxs-lookup"><span data-stu-id="50df7-184">Glob patterns</span></span>

<span data-ttu-id="50df7-185">ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="50df7-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="50df7-186">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="50df7-187">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="50df7-188">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="50df7-189">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="50df7-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="50df7-190">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="50df7-191">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="50df7-192">**glob パターンの例**</span><span class="sxs-lookup"><span data-stu-id="50df7-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="50df7-193">特定のディレクトリ内の特定のファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="50df7-194">特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="50df7-195">*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `appsettings.json` ファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="50df7-196">*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50df7-197">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="50df7-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="50df7-198">ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="50df7-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> では、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)と [Web ルート](xref:fundamentals/index#web-root)が `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="50df7-200">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="50df7-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="50df7-201">[Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="50df7-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="50df7-202">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="50df7-203">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="50df7-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="50df7-204">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="50df7-204">File Provider interfaces</span></span>

<span data-ttu-id="50df7-205">プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。</span><span class="sxs-lookup"><span data-stu-id="50df7-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="50df7-206">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="50df7-207">ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="50df7-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="50df7-208">ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="50df7-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="50df7-209">変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。</span><span class="sxs-lookup"><span data-stu-id="50df7-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="50df7-210">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="50df7-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="50df7-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="50df7-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付</span><span class="sxs-lookup"><span data-stu-id="50df7-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="50df7-213">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) メソッドを使用してファイルから読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="50df7-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="50df7-214">サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="50df7-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="50df7-215">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="50df7-215">File Provider implementations</span></span>

<span data-ttu-id="50df7-216">利用できる `IFileProvider` の実装は 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="50df7-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="50df7-217">実装</span><span class="sxs-lookup"><span data-stu-id="50df7-217">Implementation</span></span> | <span data-ttu-id="50df7-218">説明</span><span class="sxs-lookup"><span data-stu-id="50df7-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="50df7-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="50df7-220">システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="50df7-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="50df7-222">アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="50df7-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="50df7-224">コンポジット プロパイダーは、その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="50df7-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-225">PhysicalFileProvider</span></span>

<span data-ttu-id="50df7-226"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="50df7-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="50df7-227">`PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="50df7-228">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="50df7-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="50df7-229">`PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。</span><span class="sxs-lookup"><span data-stu-id="50df7-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="50df7-230">このプロバイダーを直接インスタンス化するときは、ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="50df7-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="50df7-231">次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="50df7-232">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="50df7-232">Types in the preceding example:</span></span>

* <span data-ttu-id="50df7-233">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="50df7-234">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="50df7-235">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="50df7-236">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="50df7-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="50df7-237">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="50df7-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="50df7-238">サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) を使用してプロバイダーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="50df7-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="50df7-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="50df7-240"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="50df7-241">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="50df7-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="50df7-242">埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="50df7-243">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="50df7-244">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="50df7-245">サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="50df7-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="50df7-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="50df7-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="50df7-247">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="50df7-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="50df7-248">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-248">Specify a relative file path.</span></span>
* <span data-ttu-id="50df7-249">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="50df7-250">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="50df7-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="50df7-251">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="50df7-251">Overload</span></span> | <span data-ttu-id="50df7-252">説明</span><span class="sxs-lookup"><span data-stu-id="50df7-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="50df7-253">必要に応じて相対パスのパラメーター `root` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="50df7-254">`root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="50df7-255">必要に応じて、相対パス パラメーター `root` および日付パラメーター `lastModified` (<xref:System.DateTimeOffset>) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="50df7-256">`lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="50df7-257">必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="50df7-258">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="50df7-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="50df7-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="50df7-259">CompositeFileProvider</span></span>

<span data-ttu-id="50df7-260"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="50df7-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="50df7-261">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="50df7-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="50df7-262">サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="50df7-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="50df7-263">変更の監視</span><span class="sxs-lookup"><span data-stu-id="50df7-263">Watch for changes</span></span>

<span data-ttu-id="50df7-264">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="50df7-265">`Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="50df7-266">`Watch` では <xref:Microsoft.Extensions.Primitives.IChangeToken> が返されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="50df7-267">変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-267">The change token exposes:</span></span>

* <span data-ttu-id="50df7-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="50df7-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="50df7-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="50df7-270">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="50df7-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="50df7-271">定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="50df7-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="50df7-272">サンプル アプリでは、*WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="50df7-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="50df7-273">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="50df7-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="50df7-274">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="50df7-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="50df7-275">glob パターン</span><span class="sxs-lookup"><span data-stu-id="50df7-275">Glob patterns</span></span>

<span data-ttu-id="50df7-276">ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="50df7-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="50df7-277">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="50df7-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="50df7-278">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="50df7-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="50df7-279">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="50df7-280">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="50df7-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="50df7-281">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="50df7-282">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="50df7-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="50df7-283">**glob パターンの例**</span><span class="sxs-lookup"><span data-stu-id="50df7-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="50df7-284">特定のディレクトリ内の特定のファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="50df7-285">特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="50df7-286">*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `appsettings.json` ファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="50df7-287">*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="50df7-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
