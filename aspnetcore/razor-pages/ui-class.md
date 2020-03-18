---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: ASP.NET Core のクラス ライブラリで部分ビューを使用して、再利用可能な Razor UI を作成する方法について説明します。
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650984"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="258ed-103">ASP.NET Core の Razor クラス ライブラリ プロジェクトを使用した再利用可能 UI の作成</span><span class="sxs-lookup"><span data-stu-id="258ed-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="258ed-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="258ed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="258ed-105">Razor クラス ライブラリ (RCL) には、Razor ビュー、ページ、コントローラー、ページ モデル、[Razor コンポーネント](xref:blazor/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、およびデータ モデルを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="258ed-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="258ed-106">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="258ed-107">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="258ed-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="258ed-108">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="258ed-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="258ed-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="258ed-110">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="258ed-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="258ed-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="258ed-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="258ed-112">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="258ed-113">**[Razor クラス ライブラリ]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="258ed-114">ライブラリに名前を付け ("RazorClassLib" など)、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="258ed-115">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="258ed-116">ビューをサポートする必要がある場合は、 **[ページとビューのサポート]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="258ed-117">既定では、Razor ページのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="258ed-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="258ed-118">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-118">Select **Create**.</span></span>

<span data-ttu-id="258ed-119">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="258ed-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="258ed-120">**[ページとビューのサポート]** オプションによって、ページとビューがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="258ed-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="258ed-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="258ed-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="258ed-122">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="258ed-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="258ed-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="258ed-124">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="258ed-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="258ed-125">`--support-pages-and-views` オプションを渡す (`dotnet new razorclasslib --support-pages-and-views`) と、ページとビューがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="258ed-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="258ed-126">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="258ed-127">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="258ed-128">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="258ed-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="258ed-129">ASP.NET Core テンプレートでは、RCL コンテンツが *Areas* フォルダーにあるものとしています。</span><span class="sxs-lookup"><span data-stu-id="258ed-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="258ed-130">`~/Areas/Pages` ではなく `~/Pages` でコンテンツを公開する RCL を作成する場合は、「[RCL ページのレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="258ed-131">RCL コンテンツを参照する</span><span class="sxs-lookup"><span data-stu-id="258ed-131">Reference RCL content</span></span>

<span data-ttu-id="258ed-132">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="258ed-133">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="258ed-133">NuGet package.</span></span> <span data-ttu-id="258ed-134">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="258ed-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="258ed-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="258ed-136">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="258ed-137">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="258ed-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="258ed-138">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="258ed-139">たとえば、*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* を WebApp1 に追加すると、WebApp1 の Page1 は RCL の Page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="258ed-140">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="258ed-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="258ed-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="258ed-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="258ed-142">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="258ed-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="258ed-143">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="258ed-144">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="258ed-144">RCL Pages layout</span></span>

<span data-ttu-id="258ed-145">RCL コンテンツを Web アプリの *Pages* フォルダーの一部であるかのように参照するには、次のファイル構造で RCL プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="258ed-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="258ed-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="258ed-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="258ed-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="258ed-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="258ed-148">たとえば、*RazorUIClassLib/Pages/Shared* に、 *_Header.cshtml* と *_Footer.cshtml* の 2 つの部分ファイルが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="258ed-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="258ed-149">これらの `<partial>` タグを *_Layout.cshtml* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="258ed-150">静的アセットを含む RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="258ed-150">Create an RCL with static assets</span></span>

<span data-ttu-id="258ed-151">RCL では、RCL または RCL の使用アプリで参照できる静的なコンパニオン アセットが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="258ed-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="258ed-152">ASP.NET Core では、使用アプリで利用できる静的アセットを含む RCL の作成が可能です。</span><span class="sxs-lookup"><span data-stu-id="258ed-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="258ed-153">コンパニオン アセットを RCL の一部として含めるには、クラス ライブラリに *wwwroot* フォルダーを作成し、必要なファイルをすべてそのフォルダーに含めます。</span><span class="sxs-lookup"><span data-stu-id="258ed-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="258ed-154">RCL をパックすると、*wwwroot* フォルダー内のすべてのコンパニオン アセットがパッケージに自動的に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="258ed-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="258ed-155">静的アセットを除外する</span><span class="sxs-lookup"><span data-stu-id="258ed-155">Exclude static assets</span></span>

<span data-ttu-id="258ed-156">静的アセットを除外するには、目的の除外パスをプロジェクトファイル内の `$(DefaultItemExcludes)` プロパティ グループに追加します。</span><span class="sxs-lookup"><span data-stu-id="258ed-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="258ed-157">各エントリは、セミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="258ed-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="258ed-158">次の例では、*wwwroot* フォルダー内の *lib.css* スタイルシートは、静的アセットとは見なされず、公開された RCL には組み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="258ed-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="258ed-159">Typescript の統合</span><span class="sxs-lookup"><span data-stu-id="258ed-159">Typescript integration</span></span>

<span data-ttu-id="258ed-160">TypeScript ファイルを RCL に含めるには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="258ed-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="258ed-161">TypeScript ファイル ( *.ts*) を *wwwroot* フォルダーの外側に配置します。</span><span class="sxs-lookup"><span data-stu-id="258ed-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="258ed-162">たとえば、ファイルを *Client* フォルダーに入れます。</span><span class="sxs-lookup"><span data-stu-id="258ed-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="258ed-163">*wwwroot* フォルダーの TypeScript ビルド出力を構成します。</span><span class="sxs-lookup"><span data-stu-id="258ed-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="258ed-164">プロジェクト ファイルの `PropertyGroup` の内側に `TypescriptOutDir` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="258ed-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="258ed-165">プロジェクト ファイルの `PropertyGroup` の内側に次のターゲットを追加して、TypeScript ターゲットを `ResolveCurrentProjectStaticWebAssets` ターゲットの依存関係として含めます。</span><span class="sxs-lookup"><span data-stu-id="258ed-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="258ed-166">参照されている RCL からコンテンツを使用する</span><span class="sxs-lookup"><span data-stu-id="258ed-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="258ed-167">RCL の *wwwroot* フォルダーに含まれるファイルは、`_content/{LIBRARY NAME}/` プレフィックスに基づいて RCL または使用アプリのいずれかに公開されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="258ed-168">たとえば、*Razor.Class.Lib* という名前のライブラリを使用すると、`_content/Razor.Class.Lib/` にある静的コンテンツへのパスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="258ed-169">NuGet パッケージを生成するときに、アセンブリ名がパッケージ ID と異なる場合は、`{LIBRARY NAME}` のパッケージ ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="258ed-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="258ed-170">使用アプリは、ライブラリによって提供される静的アセットを `<script>`、`<style>`、`<img>`、およびその他の HTML タグ付きで参照します。</span><span class="sxs-lookup"><span data-stu-id="258ed-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="258ed-171">使用アプリの `Startup.Configure` で [静的ファイルのサポート](xref:fundamentals/static-files)が有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="258ed-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="258ed-172">使用アプリをビルド出力から実行 (`dotnet run`) すると、開発環境で、静的な Web アセットが既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="258ed-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="258ed-173">ビルド出力から実行するときに他の環境のアセットをサポートするには、*Program.cs* のホスト ビルダーで `UseStaticWebAssets` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="258ed-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="258ed-174">発行された出力からアプリを実行する (`dotnet publish`) 場合、`UseStaticWebAssets` の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="258ed-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="258ed-175">複数プロジェクトの開発フロー</span><span class="sxs-lookup"><span data-stu-id="258ed-175">Multi-project development flow</span></span>

<span data-ttu-id="258ed-176">使用アプリの実行時は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="258ed-176">When the consuming app runs:</span></span>

* <span data-ttu-id="258ed-177">RCL のアセットは元のフォルダー内に保持されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="258ed-178">これらのアセットは、使用アプリに移行されません。</span><span class="sxs-lookup"><span data-stu-id="258ed-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="258ed-179">RCL の *wwwroot* フォルダー内の変更は、使用アプリをリビルドしなくても、RCL がリビルドされた後で、使用アプリに反映されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="258ed-180">RCL がビルドされると、静的な Web アセットの場所を記述するマニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="258ed-181">使用アプリは、実行時にそのマニフェストを読み取って、参照されているプロジェクトおよびパッケージのアセットを使用します。</span><span class="sxs-lookup"><span data-stu-id="258ed-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="258ed-182">RCL に新しいアセットが追加された場合は、使用アプリがその新しいアセットにアクセスする前に、RCL をリビルドしてそのマニフェストを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="258ed-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="258ed-183">公開</span><span class="sxs-lookup"><span data-stu-id="258ed-183">Publish</span></span>

<span data-ttu-id="258ed-184">アプリが公開されると、参照されているすべてのプロジェクトおよびパッケージのコンパニオン アセットが、`_content/{LIBRARY NAME}/` の下の公開済みアプリの *wwwroot* フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="258ed-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="258ed-185">Razor クラス ライブラリ (RCL) には、Razor ビュー、ページ、コントローラー、ページ モデル、[Razor コンポーネント](xref:blazor/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、およびデータ モデルを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="258ed-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="258ed-186">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="258ed-187">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="258ed-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="258ed-188">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="258ed-189">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="258ed-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="258ed-190">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="258ed-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="258ed-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="258ed-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="258ed-192">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="258ed-193">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="258ed-194">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="258ed-195">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="258ed-196">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="258ed-197">**[Razor クラス ライブラリ]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="258ed-198">RCL には次のプロジェクト ファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="258ed-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="258ed-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="258ed-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="258ed-200">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="258ed-201">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="258ed-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="258ed-202">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="258ed-203">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="258ed-204">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="258ed-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="258ed-205">ASP.NET Core テンプレートでは、RCL コンテンツが *Areas* フォルダーにあるものとしています。</span><span class="sxs-lookup"><span data-stu-id="258ed-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="258ed-206">`~/Areas/Pages` ではなく `~/Pages` でコンテンツを公開する RCL を作成する場合は、「[RCL ページのレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="258ed-207">RCL コンテンツを参照する</span><span class="sxs-lookup"><span data-stu-id="258ed-207">Reference RCL content</span></span>

<span data-ttu-id="258ed-208">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="258ed-209">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="258ed-209">NuGet package.</span></span> <span data-ttu-id="258ed-210">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="258ed-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="258ed-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="258ed-212">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="258ed-213">チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する</span><span class="sxs-lookup"><span data-stu-id="258ed-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="258ed-214">作成しなくても、[完全なプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="258ed-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="258ed-215">サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="258ed-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="258ed-216">GitHub の問題に関するフィードバックは[こちら](https://github.com/dotnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="258ed-217">ダウンロード アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="258ed-217">Test the download app</span></span>

<span data-ttu-id="258ed-218">完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。</span><span class="sxs-lookup"><span data-stu-id="258ed-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="258ed-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="258ed-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="258ed-220">Visual Studio で *.sln* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="258ed-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="258ed-221">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="258ed-222">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="258ed-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="258ed-223">*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="258ed-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="258ed-224">*WebApp1* ディレクトリに移動し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="258ed-225">[テスト WebApp1](#test-webapp1) の指示に従ってください。</span><span class="sxs-lookup"><span data-stu-id="258ed-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="258ed-226">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="258ed-226">Create an RCL</span></span>

<span data-ttu-id="258ed-227">このセクションでは、RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-227">In this section, an RCL is created.</span></span> <span data-ttu-id="258ed-228">Razor ファイルが RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="258ed-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="258ed-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="258ed-230">RCL プロジェクトの作成:</span><span class="sxs-lookup"><span data-stu-id="258ed-230">Create the RCL project:</span></span>

* <span data-ttu-id="258ed-231">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="258ed-232">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="258ed-233">アプリに **RazorUIClassLib** という名前を付け> **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="258ed-234">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="258ed-235">**[Razor クラス ライブラリ]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="258ed-236">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="258ed-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="258ed-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="258ed-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="258ed-238">コマンド ラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="258ed-239">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="258ed-239">The preceding commands:</span></span>

* <span data-ttu-id="258ed-240">`RazorUIClassLib` RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="258ed-241">Razor _Message ページが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="258ed-242">`-np` パラメーターによって、`PageModel` なしでページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="258ed-243">[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) ファイルが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="258ed-244">(次のセクションで追加される) Razor Pages プロジェクトのレイアウトを使用するには、 *_ViewStart.cshtml* ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="258ed-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="258ed-245">Razor のファイルとフォルダーをプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="258ed-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="258ed-246">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="258ed-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="258ed-247">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="258ed-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="258ed-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="258ed-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="258ed-249">`@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="258ed-250">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="258ed-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="258ed-251">*_ViewImports.cshtml* について詳しくは、「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="258ed-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="258ed-252">クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="258ed-253">ビルド出力には、*RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="258ed-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="258ed-254">*RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="258ed-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="258ed-255">Razor ページ プロジェクトから Razor UI ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="258ed-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="258ed-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="258ed-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="258ed-257">Razor ページ Web アプリの作成:</span><span class="sxs-lookup"><span data-stu-id="258ed-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="258ed-258">**ソリューション エクスプローラー**で、ソリューションを右クリックし、 **[追加]** > **[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="258ed-259">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="258ed-260">アプリに **WebApp1** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="258ed-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="258ed-261">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="258ed-262">**[Web アプリケーション]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="258ed-263">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="258ed-264">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[ビルド依存関係]** > **[プロジェクトの依存関係]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="258ed-265">**WebApp1** の依存関係として **RazorUIClassLib** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="258ed-266">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[追加]** > **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="258ed-267">**[参照マネージャー]** ダイアログで、 **[RazorUIClassLib]** をオンにして> **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="258ed-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="258ed-268">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="258ed-269">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="258ed-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="258ed-270">Razor Pages Web アプリと、Razor Pages アプリと RCL を含むソリューション ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="258ed-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="258ed-271">Web アプリをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="258ed-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="258ed-272">テスト WebApp1</span><span class="sxs-lookup"><span data-stu-id="258ed-272">Test WebApp1</span></span>

<span data-ttu-id="258ed-273">`/MyFeature/Page1` を参照して、Razor UI クラス ライブラリが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="258ed-274">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="258ed-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="258ed-275">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="258ed-276">たとえば、*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* を WebApp1 に追加すると、WebApp1 の Page1 は RCL の Page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="258ed-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="258ed-277">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="258ed-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="258ed-278">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="258ed-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="258ed-279">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="258ed-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="258ed-280">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="258ed-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="258ed-281">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="258ed-281">RCL Pages layout</span></span>

<span data-ttu-id="258ed-282">RCL コンテンツを Web アプリの *Pages* フォルダーの一部であるかのように参照するには、次のファイル構造で RCL プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="258ed-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="258ed-283">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="258ed-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="258ed-284">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="258ed-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="258ed-285">たとえば、*RazorUIClassLib/Pages/Shared* に、 *_Header.cshtml* と *_Footer.cshtml* の 2 つの部分ファイルが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="258ed-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="258ed-286">これらの `<partial>` タグを *_Layout.cshtml* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="258ed-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="258ed-287">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="258ed-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
