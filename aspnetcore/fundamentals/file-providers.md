---
title: ASP.NET Core でのファイル プロバイダー
author: guardrex
description: ASP.NET Core がファイル プロバイダーを使用してファイル システムへのアクセスを抽象化する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: b93b2df7fad7c173f43ad69aec865f09de6c9c34
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487577"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="c4a71-103">ASP.NET Core でのファイル プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c4a71-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="c4a71-104">作成者: [Steve Smith](https://ardalis.com/)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4a71-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4a71-105">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="c4a71-106">ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="c4a71-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) では、アプリのコンテンツ ルートと Web ルートが `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="c4a71-108">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="c4a71-109">[Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="c4a71-110">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="c4a71-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c4a71-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="c4a71-112">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="c4a71-112">File Provider interfaces</span></span>

<span data-ttu-id="c4a71-113">主要なインターフェイスは [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) です。</span><span class="sxs-lookup"><span data-stu-id="c4a71-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="c4a71-114">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="c4a71-115">ファイル情報の取得 ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo))。</span><span class="sxs-lookup"><span data-stu-id="c4a71-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="c4a71-116">ディレクトリ情報の取得 ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents))。</span><span class="sxs-lookup"><span data-stu-id="c4a71-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="c4a71-117">変更通知の設定 ([IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) を使用)。</span><span class="sxs-lookup"><span data-stu-id="c4a71-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="c4a71-118">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="c4a71-119">Exists</span><span class="sxs-lookup"><span data-stu-id="c4a71-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="c4a71-120">IsDirectory</span><span class="sxs-lookup"><span data-stu-id="c4a71-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="c4a71-121">Name</span><span class="sxs-lookup"><span data-stu-id="c4a71-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="c4a71-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="c4a71-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="c4a71-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) (日付)</span><span class="sxs-lookup"><span data-stu-id="c4a71-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="c4a71-124">[IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) メソッドを使用してファイルから読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="c4a71-125">サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="c4a71-126">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="c4a71-126">File Provider implementations</span></span>

<span data-ttu-id="c4a71-127">利用できる `IFileProvider` の実装は 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="c4a71-127">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="c4a71-128">実装</span><span class="sxs-lookup"><span data-stu-id="c4a71-128">Implementation</span></span> | <span data-ttu-id="c4a71-129">説明</span><span class="sxs-lookup"><span data-stu-id="c4a71-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="c4a71-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="c4a71-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="c4a71-131">システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="c4a71-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="c4a71-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="c4a71-133">アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="c4a71-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="c4a71-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="c4a71-135">コンポジット プロパイダーは、その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="c4a71-136">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="c4a71-136">PhysicalFileProvider</span></span>

<span data-ttu-id="c4a71-137">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) によって、物理ファイル システムへのアクセスが可能になります。</span><span class="sxs-lookup"><span data-stu-id="c4a71-137">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="c4a71-138">`PhysicalFileProvider` では (物理プロバイダーに対して) [System.IO.File](/dotnet/api/system.io.file) 型が使用され、すべてのパスのスコープが、1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-138">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="c4a71-139">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="c4a71-139">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="c4a71-140">このプロバイダーをインスタンス化する際には、ディレクトリ パスが必要です。これは、プロバイダーを使用して作成されるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-140">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="c4a71-141">`PhysicalFileProvider` プロバイダーを直接インスタンス化することも、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してコンストラクター内で `IFileProvider` を要求することもできます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-141">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="c4a71-142">**静的な型**</span><span class="sxs-lookup"><span data-stu-id="c4a71-142">**Static types**</span></span>

<span data-ttu-id="c4a71-143">次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-143">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="c4a71-144">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c4a71-144">Types in the preceding example:</span></span>

* <span data-ttu-id="c4a71-145">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="c4a71-145">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="c4a71-146">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="c4a71-146">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="c4a71-147">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="c4a71-147">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="c4a71-148">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-148">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="c4a71-149">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="c4a71-149">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="c4a71-150">サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider) を使用してプロバイダーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="c4a71-150">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="c4a71-151">**依存関係の挿入を使用してファイル プロバイダーの型を取得する**</span><span class="sxs-lookup"><span data-stu-id="c4a71-151">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="c4a71-152">プロバイダーを任意のクラスのコンストラクターに挿入し、それをローカル フィールドに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-152">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="c4a71-153">クラスのメソッド全体で、ファイルにアクセスするためにフィールドを使用します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-153">Use the field throughout the class's methods to access files.</span></span>

<span data-ttu-id="c4a71-154">サンプル アプリでは、`IndexModel` クラスは `IFileProvider` インスタンスを受け取り、アプリのベース パスに対するディレクトリのコンテンツを取得します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-154">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="c4a71-155">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c4a71-155">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c4a71-156">`IDirectoryContents` はページ内で繰り返されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-156">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="c4a71-157">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c4a71-157">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="c4a71-158">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="c4a71-158">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="c4a71-159">アセンブリ内に埋め込まれているファイルにアクセスするために、[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-159">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="c4a71-160">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-160">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="c4a71-161">埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-161">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="c4a71-162">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-162">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="c4a71-163">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-163">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="c4a71-164">サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-164">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="c4a71-165">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c4a71-165">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="c4a71-166">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="c4a71-166">Additional overloads allow you to:</span></span>

* <span data-ttu-id="c4a71-167">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-167">Specify a relative file path.</span></span>
* <span data-ttu-id="c4a71-168">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-168">Scope files to a last modified date.</span></span>
* <span data-ttu-id="c4a71-169">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-169">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="c4a71-170">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="c4a71-170">Overload</span></span> | <span data-ttu-id="c4a71-171">説明</span><span class="sxs-lookup"><span data-stu-id="c4a71-171">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="c4a71-172">ManifestEmbeddedFileProvider(Assembly, String)</span><span class="sxs-lookup"><span data-stu-id="c4a71-172">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="c4a71-173">必要に応じて相対パスのパラメーター `root` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-173">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="c4a71-174">`root` を指定して、[GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-174">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="c4a71-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="c4a71-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="c4a71-176">必要に応じて、相対パスのパラメーター `root` と、`lastModified` 日付 ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) パラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-176">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="c4a71-177">`lastModified` の日付は、[IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) によって返される [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-177">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="c4a71-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="c4a71-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="c4a71-179">必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-179">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="c4a71-180">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-180">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="c4a71-181">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="c4a71-181">CompositeFileProvider</span></span>

<span data-ttu-id="c4a71-182">[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-182">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="c4a71-183">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-183">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="c4a71-184">サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-184">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="c4a71-185">変更の監視</span><span class="sxs-lookup"><span data-stu-id="c4a71-185">Watch for changes</span></span>

<span data-ttu-id="c4a71-186">[IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-186">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="c4a71-187">`Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-187">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="c4a71-188">`Watch` によって [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) が返されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-188">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="c4a71-189">変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-189">The change token exposes:</span></span>

* <span data-ttu-id="c4a71-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="c4a71-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): 指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="c4a71-192">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="c4a71-192">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="c4a71-193">定数の監視を有効に使用するには、次に示すように [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) を使用するか、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-193">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="c4a71-194">サンプル アプリでは、*WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="c4a71-194">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="c4a71-195">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c4a71-195">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="c4a71-196">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="c4a71-196">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="c4a71-197">glob パターン</span><span class="sxs-lookup"><span data-stu-id="c4a71-197">Glob patterns</span></span>

<span data-ttu-id="c4a71-198">ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-198">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="c4a71-199">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-199">Specify groups of files with these patterns.</span></span> <span data-ttu-id="c4a71-200">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="c4a71-200">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="c4a71-201">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-201">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="c4a71-202">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-202">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="c4a71-203">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-203">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="c4a71-204">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c4a71-204">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="c4a71-205">**glob パターンの例**</span><span class="sxs-lookup"><span data-stu-id="c4a71-205">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="c4a71-206">特定のディレクトリ内の特定のファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-206">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="c4a71-207">特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-207">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="c4a71-208">*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `appsettings.json` ファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-208">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="c4a71-209">*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="c4a71-209">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
