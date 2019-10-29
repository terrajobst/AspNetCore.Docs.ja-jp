---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: ASP.NET Core のクラスライブラリで部分ビューを使用して、再利用可能な Razor UI を作成する方法について説明します。
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: ff12eea5406c4f5392a466728741000e3dd16fc1
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034234"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="5ccf6-103">ASP.NET Core の Razor クラスライブラリプロジェクトを使用して、再利用可能な UI を作成する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="5ccf6-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5ccf6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ccf6-105">Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="5ccf6-106">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="5ccf6-107">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="5ccf6-108">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="5ccf6-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="5ccf6-110">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5ccf6-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ccf6-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5ccf6-112">Visual Studio から、[新しい**プロジェクトの作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="5ccf6-113">**[Razor クラスライブラリ]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="5ccf6-114">ライブラリに名前を指定します (たとえば、"RazorClassLib")。**作成**> ます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="5ccf6-115">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="5ccf6-116">ビューをサポートする必要がある場合は **、サポートページとビュー**を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="5ccf6-117">既定では、Razor Pages のみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="5ccf6-118">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-118">Select **Create**.</span></span>

<span data-ttu-id="5ccf6-119">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="5ccf6-120">**[サポートページとビュー]** オプションでは、ページとビューがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5ccf6-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5ccf6-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5ccf6-122">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="5ccf6-123">(例:</span><span class="sxs-lookup"><span data-stu-id="5ccf6-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="5ccf6-124">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="5ccf6-125">`--support-pages-and-views` オプション (`dotnet new razorclasslib --support-pages-and-views`) を渡して、ページとビューのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="5ccf6-126">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="5ccf6-127">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="5ccf6-128">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="5ccf6-129">ASP.NET Core テンプレートでは、RCL コンテンツが*Areas*フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="5ccf6-130">`~/Areas/Pages`ではなく `~/Pages` でコンテンツを公開する RCL を作成するには、「 [Rcl ページレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="5ccf6-131">RCL コンテンツの参照</span><span class="sxs-lookup"><span data-stu-id="5ccf6-131">Reference RCL content</span></span>

<span data-ttu-id="5ccf6-132">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="5ccf6-133">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-133">NuGet package.</span></span> <span data-ttu-id="5ccf6-134">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="5ccf6-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5ccf6-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="5ccf6-136">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="5ccf6-137">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="5ccf6-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="5ccf6-138">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="5ccf6-139">たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="5ccf6-140">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="5ccf6-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="5ccf6-142">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="5ccf6-143">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="5ccf6-144">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="5ccf6-144">RCL Pages layout</span></span>

<span data-ttu-id="5ccf6-145">RCL コンテンツを web アプリの*Pages*フォルダーの一部として参照するには、次のファイル構造で rcl プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="5ccf6-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="5ccf6-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="5ccf6-148">たとえば、 *RazorUIClassLib/Pages/Shared*に2つの部分ファイルが含まれている*とし* *ます。*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="5ccf6-149">`<partial>` タグを*Layout*ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="5ccf6-150">静的なアセットを含む RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-150">Create an RCL with static assets</span></span>

<span data-ttu-id="5ccf6-151">RCL では、RCL の消費アプリから参照できる、関連する静的アセットが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-151">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="5ccf6-152">ASP.NET Core では、使用中のアプリで利用可能な静的アセットを含む RCLs を作成できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="5ccf6-153">RCL の一部としてコンパニオン資産を含めるには、クラスライブラリに*wwwroot*フォルダーを作成し、そのフォルダーに必要なファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="5ccf6-154">RCL をパッキングすると、 *wwwroot*フォルダー内のすべてのコンパニオン資産がパッケージに自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="5ccf6-155">静的アセットを除外する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-155">Exclude static assets</span></span>

<span data-ttu-id="5ccf6-156">静的アセットを除外するには、目的の除外パスをプロジェクトファイルの `$(DefaultItemExcludes)` プロパティグループに追加します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="5ccf6-157">エントリをセミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="5ccf6-158">次の例では、 *wwwroot*フォルダーの *.lib*スタイルシートは静的アセットとは見なされず、公開された rcl には含まれていません。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="5ccf6-159">Typescript の統合</span><span class="sxs-lookup"><span data-stu-id="5ccf6-159">Typescript integration</span></span>

<span data-ttu-id="5ccf6-160">TypeScript ファイルを RCL に含めるには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="5ccf6-161">TypeScript ファイル (*ts*) を*wwwroot*フォルダーの外側に配置します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="5ccf6-162">たとえば、ファイルを*クライアント*フォルダーに配置します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="5ccf6-163">*Wwwroot*フォルダーの TypeScript ビルド出力を構成します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="5ccf6-164">プロジェクトファイルの `PropertyGroup` 内の `TypescriptOutDir` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="5ccf6-165">プロジェクトファイル内の `PropertyGroup` 内に次のターゲットを追加することにより、TypeScript ターゲットを `ResolveCurrentProjectStaticWebAssets` ターゲットの依存関係として含めます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="5ccf6-166">参照されている RCL からのコンテンツの使用</span><span class="sxs-lookup"><span data-stu-id="5ccf6-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="5ccf6-167">RCL の*wwwroot*フォルダーに含まれるファイルは、使用中のアプリにプレフィックス `_content/{LIBRARY NAME}/`で公開されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-167">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="5ccf6-168">たとえば、という名前のライブラリを使用*すると、* `_content/Razor.Class.Lib/`の静的コンテンツへのパスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="5ccf6-169">使用中のアプリは、ライブラリによって提供される静的なアセットを `<script>`、`<style>`、`<img>`、およびその他の HTML タグと共に参照します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-169">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="5ccf6-170">コンシューマーアプリの `Startup.Configure`では、[静的ファイルのサポート](xref:fundamentals/static-files)が有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-170">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="5ccf6-171">ビルド出力 (`dotnet run`) から使用中のアプリを実行すると、開発環境では、静的な web アセットが既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-171">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="5ccf6-172">ビルド出力から実行するときに他の環境のアセットをサポートするには、 *Program.cs*のホストビルダーで `UseStaticWebAssets` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-172">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="5ccf6-173">発行された出力 (`dotnet publish`) からアプリを実行する場合、`UseStaticWebAssets` の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-173">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="5ccf6-174">複数プロジェクトの開発フロー</span><span class="sxs-lookup"><span data-stu-id="5ccf6-174">Multi-project development flow</span></span>

<span data-ttu-id="5ccf6-175">コンシューマーアプリの実行時:</span><span class="sxs-lookup"><span data-stu-id="5ccf6-175">When the consuming app runs:</span></span>

* <span data-ttu-id="5ccf6-176">RCL のアセットは元のフォルダーにとどまります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-176">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="5ccf6-177">アセットは、消費側のアプリに移動されません。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-177">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="5ccf6-178">Rcl の*wwwroot*フォルダー内の変更は、rcl がリビルドされた後、使用中のアプリを再構築せずに、消費側アプリに反映されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-178">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="5ccf6-179">RCL がビルドされると、静的な web 資産の場所を記述するマニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-179">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="5ccf6-180">コンシューマーアプリは、実行時にマニフェストを読み取り、参照されたプロジェクトとパッケージのアセットを使用します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-180">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="5ccf6-181">RCL に新しいアセットが追加されたときに、使用中のアプリが新しい資産にアクセスできるようにするには、そのマニフェストを更新するために RCL を再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-181">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="5ccf6-182">公開</span><span class="sxs-lookup"><span data-stu-id="5ccf6-182">Publish</span></span>

<span data-ttu-id="5ccf6-183">アプリが発行されると、参照されているすべてのプロジェクトおよびパッケージの関連する資産が、`_content/{LIBRARY NAME}/`の下の発行済みアプリの*wwwroot*フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-183">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5ccf6-184">Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-184">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="5ccf6-185">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-185">The RCL can be packaged and reused.</span></span> <span data-ttu-id="5ccf6-186">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-186">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="5ccf6-187">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-187">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="5ccf6-188">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-188">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="5ccf6-189">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-189">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5ccf6-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ccf6-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5ccf6-191">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-191">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5ccf6-192">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-192">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5ccf6-193">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-193">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="5ccf6-194">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-194">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="5ccf6-195">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-195">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="5ccf6-196">[ **Razor クラスライブラリ** **] > [OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-196">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="5ccf6-197">RCL には、次のプロジェクトファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-197">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5ccf6-198">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5ccf6-198">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5ccf6-199">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-199">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="5ccf6-200">(例:</span><span class="sxs-lookup"><span data-stu-id="5ccf6-200">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="5ccf6-201">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-201">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="5ccf6-202">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-202">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="5ccf6-203">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-203">Add Razor files to the RCL.</span></span>

<span data-ttu-id="5ccf6-204">ASP.NET Core テンプレートでは、RCL コンテンツが*Areas*フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-204">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="5ccf6-205">`~/Areas/Pages`ではなく `~/Pages` でコンテンツを公開する RCL を作成するには、「 [Rcl ページレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-205">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="5ccf6-206">RCL コンテンツの参照</span><span class="sxs-lookup"><span data-stu-id="5ccf6-206">Reference RCL content</span></span>

<span data-ttu-id="5ccf6-207">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-207">The RCL can be referenced by:</span></span>

* <span data-ttu-id="5ccf6-208">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-208">NuGet package.</span></span> <span data-ttu-id="5ccf6-209">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-209">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="5ccf6-210">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5ccf6-210">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="5ccf6-211">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-211">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="5ccf6-212">チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-212">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="5ccf6-213">作成しなくても、[完全なプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-213">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="5ccf6-214">サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-214">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="5ccf6-215">GitHub の問題に関するフィードバックは[こちら](https://github.com/aspnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-215">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="5ccf6-216">ダウンロード アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="5ccf6-216">Test the download app</span></span>

<span data-ttu-id="5ccf6-217">完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-217">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5ccf6-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ccf6-218">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5ccf6-219">Visual Studio で *.sln* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-219">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="5ccf6-220">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-220">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5ccf6-221">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5ccf6-221">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5ccf6-222">*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-222">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="5ccf6-223">*WebApp1* ディレクトリに移動し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-223">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="5ccf6-224">[テスト WebApp1](#test-webapp1) の指示に従ってください。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-224">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="5ccf6-225">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-225">Create an RCL</span></span>

<span data-ttu-id="5ccf6-226">このセクションでは、RCL を作成します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-226">In this section, an RCL is created.</span></span> <span data-ttu-id="5ccf6-227">Razor ファイルが RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-227">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5ccf6-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ccf6-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5ccf6-229">RCL プロジェクトの作成:</span><span class="sxs-lookup"><span data-stu-id="5ccf6-229">Create the RCL project:</span></span>

* <span data-ttu-id="5ccf6-230">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-230">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5ccf6-231">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-231">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5ccf6-232">アプリに**RazorUIClassLib** > **OK**という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-232">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="5ccf6-233">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-233">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="5ccf6-234">[ **Razor クラスライブラリ** **] > [OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-234">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="5ccf6-235">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-235">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5ccf6-236">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5ccf6-236">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5ccf6-237">コマンド ラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-237">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="5ccf6-238">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-238">The preceding commands:</span></span>

* <span data-ttu-id="5ccf6-239">RCL `RazorUIClassLib` を作成します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-239">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="5ccf6-240">Razor _Message ページが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-240">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="5ccf6-241">`-np` パラメーターによって、`PageModel` なしでページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-241">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="5ccf6-242">[Viewstart. cshtml](xref:mvc/views/layout#running-code-before-each-view)ファイルを作成し、rcl に追加します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-242">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="5ccf6-243">Razor Pages プロジェクト (次のセクションで追加される) のレイアウトを使用するには、ファイル ( *_l* ) が必要です。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-243">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="5ccf6-244">Razor ファイルとフォルダーをプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="5ccf6-244">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="5ccf6-245">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="5ccf6-246">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="5ccf6-247">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-247">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="5ccf6-248">`@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-248">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5ccf6-249">(例:</span><span class="sxs-lookup"><span data-stu-id="5ccf6-249">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="5ccf6-250">_ViewImports の詳細については、「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」を参照してください *。*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-250">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="5ccf6-251">クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-251">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="5ccf6-252">ビルド出力には、*RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-252">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="5ccf6-253">*RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-253">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="5ccf6-254">Razor ページ プロジェクトから Razor UI ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-254">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5ccf6-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ccf6-255">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5ccf6-256">Razor ページ Web アプリの作成:</span><span class="sxs-lookup"><span data-stu-id="5ccf6-256">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="5ccf6-257">**ソリューションエクスプローラー**で、ソリューションを右クリックして >**新しいプロジェクト**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-257">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="5ccf6-258">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-258">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5ccf6-259">アプリに **WebApp1** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-259">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="5ccf6-260">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-260">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="5ccf6-261">[ **Web アプリケーション**> **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-261">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="5ccf6-262">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-262">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="5ccf6-263">**ソリューションエクスプローラー**で、 **[WebApp1]** を右クリックし、[**ビルドの依存関係**>**プロジェクトの依存関係**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-263">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="5ccf6-264">**WebApp1** の依存関係として **RazorUIClassLib** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-264">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="5ccf6-265">**ソリューションエクスプローラー**で、 **WebApp1**を右クリックし、[>**参照**の**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-265">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="5ccf6-266">**参照マネージャー** ダイアログボックスで、 **RazorUIClassLib** > **OK**をオンにします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-266">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="5ccf6-267">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-267">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5ccf6-268">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5ccf6-268">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5ccf6-269">Razor Pages web アプリと、Razor Pages アプリと RCL を含むソリューションファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-269">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="5ccf6-270">Web アプリをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-270">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="5ccf6-271">テスト WebApp1</span><span class="sxs-lookup"><span data-stu-id="5ccf6-271">Test WebApp1</span></span>

<span data-ttu-id="5ccf6-272">`/MyFeature/Page1` を参照して、Razor UI クラスライブラリが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-272">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="5ccf6-273">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="5ccf6-273">Override views, partial views, and pages</span></span>

<span data-ttu-id="5ccf6-274">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-274">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="5ccf6-275">たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-275">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="5ccf6-276">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-276">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="5ccf6-277">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-277">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="5ccf6-278">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-278">Update the markup to indicate the new location.</span></span> <span data-ttu-id="5ccf6-279">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-279">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="5ccf6-280">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="5ccf6-280">RCL Pages layout</span></span>

<span data-ttu-id="5ccf6-281">RCL コンテンツを web アプリの*Pages*フォルダーの一部として参照するには、次のファイル構造で rcl プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-281">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="5ccf6-282">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-282">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="5ccf6-283">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-283">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="5ccf6-284">たとえば、 *RazorUIClassLib/Pages/Shared*に2つの部分ファイルが含まれている*とし* *ます。*</span><span class="sxs-lookup"><span data-stu-id="5ccf6-284">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="5ccf6-285">`<partial>` タグを*Layout*ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="5ccf6-285">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
