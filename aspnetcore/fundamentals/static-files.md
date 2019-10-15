---
title: ASP.NET Core の静的ファイル
author: rick-anderson
description: 静的ファイルを提供したり、それをセキュリティで保護したりする方法、および ASP.NET Core Web アプリで静的ファイルをホストするミドルウェアの動作を構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 2f153551a86860616469200862723528e4a8cc1c
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007328"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="7c4c4-103">ASP.NET Core の静的ファイル</span><span class="sxs-lookup"><span data-stu-id="7c4c4-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="7c4c4-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7c4c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7c4c4-105">HTML、CSS、画像、JavaScript などの静的ファイルは、ASP.NET Core アプリにより直接クライアントに提供される資産です。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="7c4c4-106">これらのファイルを提供するには、いくつかの構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="7c4c4-107">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="7c4c4-108">静的ファイルの提供</span><span class="sxs-lookup"><span data-stu-id="7c4c4-108">Serve static files</span></span>

<span data-ttu-id="7c4c4-109">静的ファイルは、プロジェクトの [Web ルート](xref:fundamentals/index#web-root) ディレクトリ内に格納されています。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="7c4c4-110">既定のディレクトリは *{content root}/wwwroot* ですが、[UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) メソッドを使用して変更できます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="7c4c4-111">詳細については、「[コンテンツ ルート](xref:fundamentals/index#content-root)」および「[Web ルート](xref:fundamentals/index#web-root)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="7c4c4-112">アプリの Web ホストでは、コンテンツのルート ディレクトリが把握されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7c4c4-113">`WebHost.CreateDefaultBuilder` メソッドでは、コンテンツのルートが現在のディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c4c4-114">`Program.Main` 内で [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) が呼び出され、コンテンツのルートが現在のディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="7c4c4-115">静的ファイルには、[Web ルート](xref:fundamentals/index#web-root)に対する相対パスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7c4c4-116">たとえば、**Web アプリケーション** プロジェクト テンプレートには、*wwwroot* フォルダー内の次のいくつかフォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="7c4c4-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-117">**wwwroot**</span></span>
  * <span data-ttu-id="7c4c4-118">**css**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-118">**css**</span></span>
  * <span data-ttu-id="7c4c4-119">**images**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-119">**images**</span></span>
  * <span data-ttu-id="7c4c4-120">**js**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-120">**js**</span></span>

<span data-ttu-id="7c4c4-121">*images* サブフォルダーのファイルにアクセスするための URI は、*http://\<server_address>/images/\<image_file_name>* です。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="7c4c4-122">たとえば、 *http://localhost:9189/images/banner3.svg* です。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c4c4-123">.NET Framework を対象としている場合、プロジェクトに [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="7c4c4-124">.NET Core をターゲットとする場合、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)にこのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7c4c4-125">.NET Framework を対象としている場合、プロジェクトに [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="7c4c4-126">.NET Core をターゲットとする場合、[Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)にこのパッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c4c4-127">プロジェクトに、[Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="7c4c4-128">静的ファイル サービスの提供を可能にする、[ミドルウェア](xref:fundamentals/middleware/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="7c4c4-129">Web ルート内のファイルの提供</span><span class="sxs-lookup"><span data-stu-id="7c4c4-129">Serve files inside of web root</span></span>

<span data-ttu-id="7c4c4-130">`Startup.Configure` 内の [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="7c4c4-131">`UseStaticFiles` メソッドのパラメーターなしのオーバーロードによって、[Web ルート](xref:fundamentals/index#web-root)内のファイルが提供可能とマークされます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="7c4c4-132">次のマークアップは、*wwwroot/images/banner1.svg* を参照します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="7c4c4-133">上記のコードでは、チルダ文字 `~/` が [Web ルート](xref:fundamentals/index#web-root)を指します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="7c4c4-134">Web ルート外のファイルの提供</span><span class="sxs-lookup"><span data-stu-id="7c4c4-134">Serve files outside of web root</span></span>

<span data-ttu-id="7c4c4-135">提供する静的ファイルが [Web ルート](xref:fundamentals/index#web-root)の外にあるディレクトリ階層について考えます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="7c4c4-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-136">**wwwroot**</span></span>
  * <span data-ttu-id="7c4c4-137">**css**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-137">**css**</span></span>
  * <span data-ttu-id="7c4c4-138">**images**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-138">**images**</span></span>
  * <span data-ttu-id="7c4c4-139">**js**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-139">**js**</span></span>
* <span data-ttu-id="7c4c4-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="7c4c4-141">**images**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-141">**images**</span></span>
    * <span data-ttu-id="7c4c4-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-142">*banner1.svg*</span></span>

<span data-ttu-id="7c4c4-143">静的ファイル ミドルウェアを次のように構成すると、要求で *banner1.svg* ファイルにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="7c4c4-144">前述のコードでは、*MyStaticFiles* ディレクトリ階層は、*StaticFiles* URI セグメントでパブリックに公開されています。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="7c4c4-145">*http://\<server_address>/StaticFiles/images/banner1.svg* に対する要求によって、*banner1.svg* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="7c4c4-146">次のマークアップは、*MyStaticFiles/images/banner1.svg* を参照します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="7c4c4-147">HTTP 応答ヘッダーの設定</span><span class="sxs-lookup"><span data-stu-id="7c4c4-147">Set HTTP response headers</span></span>

<span data-ttu-id="7c4c4-148">[StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) オブジェクトを使用すると、HTTP 応答ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="7c4c4-149">[Web ルート](xref:fundamentals/index#web-root)から提供される静的ファイルの構成に加えて、次のコードで `Cache-Control` ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="7c4c4-150">[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) メソッドは、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="7c4c4-151">これらのファイルは、開発環境で 10 分間 (600 秒) パブリックにキャッシュ可能でした。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Cache-Control ヘッダーが追加された応答ヘッダー](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="7c4c4-153">静的ファイルの承認</span><span class="sxs-lookup"><span data-stu-id="7c4c4-153">Static file authorization</span></span>

<span data-ttu-id="7c4c4-154">静的ファイル ミドルウェアでは、承認の確認は行いません。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="7c4c4-155">*wwwroot* の下にあるものも含め、このミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="7c4c4-156">承認に基づいてファイルを提供するには:</span><span class="sxs-lookup"><span data-stu-id="7c4c4-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="7c4c4-157">*wwwroot* や静的ファイル ミドルウェアがアクセスできる任意のディレクトリの外にファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="7c4c4-158">承認が適用されるアクション メソッドを使用して提供するようにします。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="7c4c4-159">[FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="7c4c4-160">ディレクトリ参照の有効化</span><span class="sxs-lookup"><span data-stu-id="7c4c4-160">Enable directory browsing</span></span>

<span data-ttu-id="7c4c4-161">ディレクトリ参照では、Web アプリのユーザーが、ディレクトリ一覧および、指定したディレクトリ内のファイルを参照できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="7c4c4-162">ディレクトリ参照は、セキュリティ上の理由から既定で無効になっています (「[注意点](#considerations)」を参照)。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="7c4c4-163">`Startup.Configure` で [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) メソッドを呼び出すと、ディレクトリの参照を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="7c4c4-164">`Startup.ConfigureServices` から [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) メソッドを呼び出すと、必要なサービスを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="7c4c4-165">上記のコードでは、URL *http://\<server_address>/MyImages* が使用され、各ファイルおよびフォルダーへのリンクを含む *wwwroot/images* フォルダーのディレクトリが参照できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![ディレクトリ参照](static-files/_static/dir-browse.png)

<span data-ttu-id="7c4c4-167">参照を有効にした場合のセキュリティ上のリスクについては、「[注意点](#considerations)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="7c4c4-168">次の例の 2 つの `UseStaticFiles` 呼び出しを確認してください。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="7c4c4-169">最初の呼び出しにより、*wwwroot* フォルダー内の静的ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="7c4c4-170">2 番目の呼び出しにより、URL *http://\<server_address>/MyImages* が使用され、*wwwroot/images* フォルダーのディレクトリ参照が有効になります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="7c4c4-171">既定のドキュメントの提供</span><span class="sxs-lookup"><span data-stu-id="7c4c4-171">Serve a default document</span></span>

<span data-ttu-id="7c4c4-172">設定された既定のホーム ページは、サイトを訪問するユーザーの論理的な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="7c4c4-173">ユーザーが URI を完全修飾しない場合に既定のページを提供するには、`Startup.Configure` から [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="7c4c4-174">既定のファイルを提供するには、`UseStaticFiles` の前に `UseDefaultFiles` が呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="7c4c4-175">`UseDefaultFiles` は、ファイルを実際には提供しない URL リライターです。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="7c4c4-176">ファイルを提供するには、`UseStaticFiles` を使用して静的ファイル ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="7c4c4-177">`UseDefaultFiles` を使用し、以下のフォルダーを検索します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="7c4c4-178">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-178">*default.htm*</span></span>
* <span data-ttu-id="7c4c4-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-179">*default.html*</span></span>
* <span data-ttu-id="7c4c4-180">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-180">*index.htm*</span></span>
* <span data-ttu-id="7c4c4-181">*index.html*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-181">*index.html*</span></span>

<span data-ttu-id="7c4c4-182">一覧で見つかった最初のファイルは、要求で完全修飾 URI が使用されたかのように提供されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="7c4c4-183">ブラウザー URL は、要求された URI を反映し続けます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="7c4c4-184">次のコードによって、既定のファイル名が *mydefault.html* に変更されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="7c4c4-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="7c4c4-185">UseFileServer</span></span>

<span data-ttu-id="7c4c4-186">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) は、`UseStaticFiles`、`UseDefaultFiles`、`UseDirectoryBrowser` の機能を兼ね備えています。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-186">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="7c4c4-187">次のコードによって、静的ファイルと既定ファイルが提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="7c4c4-188">ディレクトリ参照は有効にしません。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="7c4c4-189">次のコードは、ディレクトリ参照が有効になった、パラメーターなしのオーバーロード時に構築されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="7c4c4-190">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="7c4c4-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-191">**wwwroot**</span></span>
  * <span data-ttu-id="7c4c4-192">**css**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-192">**css**</span></span>
  * <span data-ttu-id="7c4c4-193">**images**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-193">**images**</span></span>
  * <span data-ttu-id="7c4c4-194">**js**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-194">**js**</span></span>
* <span data-ttu-id="7c4c4-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="7c4c4-196">**images**</span><span class="sxs-lookup"><span data-stu-id="7c4c4-196">**images**</span></span>
    * <span data-ttu-id="7c4c4-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-197">*banner1.svg*</span></span>
  * <span data-ttu-id="7c4c4-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-198">*default.html*</span></span>

<span data-ttu-id="7c4c4-199">次のコードにより、`MyStaticFiles` への静的ファイル、既定ファイル、ディレクトリ参照が有効になります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="7c4c4-200">`EnableDirectoryBrowsing` プロパティの値が `true` であるとき、`AddDirectoryBrowser` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="7c4c4-201">このファイル階層と前述のコードを使用すると、URL は次のように解決されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="7c4c4-202">URI</span><span class="sxs-lookup"><span data-stu-id="7c4c4-202">URI</span></span>            |                             <span data-ttu-id="7c4c4-203">応答</span><span class="sxs-lookup"><span data-stu-id="7c4c4-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="7c4c4-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="7c4c4-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="7c4c4-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="7c4c4-206">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="7c4c4-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="7c4c4-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="7c4c4-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="7c4c4-208">*MyStaticFiles* ディレクトリに既定の名前のファイルが存在しない場合、*http://\<server_address>/StaticFiles* によってクリック可能なリンクを含むディレクトリの一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![静的ファイルの一覧](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="7c4c4-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> および <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> では、`http://{SERVER ADDRESS}/StaticFiles` (末尾のスラッシュなし) から `http://{SERVER ADDRESS}/StaticFiles/` (末尾のスラッシュあり) へのクライアント側リダイレクトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="7c4c4-211">*StaticFiles* ディレクトリ内の相対 URL は、末尾のスラッシュがないと無効です。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="7c4c4-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="7c4c4-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="7c4c4-213">[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) クラスには、MIME コンテンツ タイプへのファイル拡張子のマッピングを行う `Mappings` プロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="7c4c4-214">次の例では、いくつかのファイル拡張子は、既知の MIME タイプに登録されています。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="7c4c4-215">*.rtf* 拡張子は置換され、 *.mp4* は削除されています。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="7c4c4-216">「[MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml)」 (MIME コンテンツ タイプ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="7c4c4-217">非標準のコンテンツ タイプ</span><span class="sxs-lookup"><span data-stu-id="7c4c4-217">Non-standard content types</span></span>

<span data-ttu-id="7c4c4-218">静的ファイル ミドルウェアでは、約 400 個の既知のファイル コンテンツ タイプが認識されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="7c4c4-219">ユーザーがファイルの種類が不明なファイルを要求した場合、静的ファイル ミドルウェアでその要求がパイプラインの次のミドルウェアに渡されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="7c4c4-220">ミドルウェアで要求が処理されない場合、*404 見つかりません* という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="7c4c4-221">ディレクトリ参照が有効になっている場合、ディレクトリ一覧にファイルへのリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="7c4c4-222">次のコードによって、不明なタイプを提供できるようにし、不明なファイルをイメージとしてレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="7c4c4-223">上記のコードでは、不明なコンテンツ タイプ ファイルに対する要求は、イメージとして返されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="7c4c4-224">[ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) を有効にすると、セキュリティ上リスクとなります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="7c4c4-225">これは既定では無効で、使用は推奨されていません。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="7c4c4-226">非標準の拡張子のファイルを提供する場合、より安全な代替となるのは、[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) です。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="7c4c4-227">注意事項</span><span class="sxs-lookup"><span data-stu-id="7c4c4-227">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="7c4c4-228">`UseDirectoryBrowser` と `UseStaticFiles` では、機密データが漏洩することがあります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-228">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="7c4c4-229">本番では、ディレクトリ参照を無効にすることが、強く推奨されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-229">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="7c4c4-230">`UseStaticFiles` や `UseDirectoryBrowser` でどのディレクトリが有効になっているか、慎重にご確認ください。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-230">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="7c4c4-231">ディレクトリ全体とそのサブディレクトリが、パブリックにアクセス可能になります。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-231">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="7c4c4-232">ファイルは、パブリックに提供するのに適した、 *\<content_root>/wwwroot* などの専用ディレクトリに格納します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-232">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="7c4c4-233">これらのファイルは、MVC ビュー、Razor ページ (2.x のみ)、構成ファイルなどとは別にします。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-233">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="7c4c4-234">`UseDirectoryBrowser` と `UseStaticFiles` で公開されるコンテンツの URL では、大文字と小文字が区別され、基になるファイル システムの文字制限の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-234">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="7c4c4-235">たとえば、Windows では大文字小文字は区別されますが、macOS と Linux ではされません。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-235">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="7c4c4-236">IIS でホストされている ASP.NET Core アプリは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して、静的ファイルの要求を含む、すべての要求をアプリに転送します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-236">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="7c4c4-237">IIS 静的ファイル ハンドラーは使用されません。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-237">The IIS static file handler isn't used.</span></span> <span data-ttu-id="7c4c4-238">モジュールによって処理される前に、これで要求が処理されることはありません。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-238">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="7c4c4-239">IIS マネージャーで次の手順を実行し、サーバーまたは Web サイト レベルで IIS の静的ファイル ハンドラーを削除します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-239">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="7c4c4-240">**[モジュール]** 機能に移動します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-240">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="7c4c4-241">一覧の **[StaticFileModule]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-241">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="7c4c4-242">**[アクション]** サイドバーで、 **[削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-242">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="7c4c4-243">IIS の静的ファイル ハンドラーが有効になっており、**かつ**、ASP.NET Core モジュールが正しく構成されていない場合、静的ファイルにサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-243">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="7c4c4-244">これは、たとえば、*web.config* ファイルが配置されていない場合などで発生します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-244">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="7c4c4-245">アプリ プロジェクトの [Web ルート](xref:fundamentals/index#web-root)の外に、コード ファイル ( *.cs* と *.cshtml* を含む) を配置します。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-245">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="7c4c4-246">これにより、アプリのクライアント側コンテンツとサーバー ベースのコードの間で、論理的な分離が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-246">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="7c4c4-247">これによって、サーバー側のコードが漏洩するのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="7c4c4-247">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c4c4-248">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7c4c4-248">Additional resources</span></span>

* [<span data-ttu-id="7c4c4-249">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="7c4c4-249">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="7c4c4-250">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="7c4c4-250">Introduction to ASP.NET Core</span></span>](xref:index)
