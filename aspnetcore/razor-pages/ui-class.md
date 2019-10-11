---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: Razor を ASP.NET core クラス ライブラリの部分ビューを使用して再利用可能な UI を作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/08/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: d656e924033f1b217cdd8c86f7d00411c5d71beb
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037588"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="c1d29-103">ASP.NET Core の Razor クラスライブラリプロジェクトを使用して、再利用可能な UI を作成する</span><span class="sxs-lookup"><span data-stu-id="c1d29-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="c1d29-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c1d29-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1d29-105">Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="c1d29-106">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="c1d29-107">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="c1d29-108">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="c1d29-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c1d29-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="c1d29-110">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="c1d29-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1d29-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d29-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1d29-112">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-112">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c1d29-113">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c1d29-114">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-114">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="c1d29-115">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="c1d29-116">**ASP.NET Core 3.0**以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-116">Verify **ASP.NET Core 3.0** or later is selected.</span></span>
* <span data-ttu-id="c1d29-117">[ **Razor クラスライブラリ**> **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-117">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="c1d29-118">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="c1d29-118">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="c1d29-119">Visual Studio のテンプレートオプションを使用すると、ページとビューのテンプレートをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-119">A template option in Visual Studio provides template support for pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c1d29-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c1d29-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c1d29-121">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="c1d29-122">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-122">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="c1d29-123">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="c1d29-123">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="c1d29-124">@No__t-0 オプション (`dotnet new razorclasslib --support-pages-and-views`) を渡して、ページとビューのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-124">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="c1d29-125">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-125">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="c1d29-126">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-126">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="c1d29-127">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-127">Add Razor files to the RCL.</span></span>

<span data-ttu-id="c1d29-128">ASP.NET Core テンプレートは、RCL コンテンツが前提としています。、*領域*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="c1d29-128">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="c1d29-129">@No__t ではなく `~/Pages` でコンテンツを公開する RCL を作成するには、「 [Rcl ページレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-129">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="c1d29-130">RCL コンテンツの参照</span><span class="sxs-lookup"><span data-stu-id="c1d29-130">Reference RCL content</span></span>

<span data-ttu-id="c1d29-131">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-131">The RCL can be referenced by:</span></span>

* <span data-ttu-id="c1d29-132">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="c1d29-132">NuGet package.</span></span> <span data-ttu-id="c1d29-133">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-133">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="c1d29-134">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c1d29-134">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="c1d29-135">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-135">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="c1d29-136">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="c1d29-136">Override views, partial views, and pages</span></span>

<span data-ttu-id="c1d29-137">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-137">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="c1d29-138">たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-138">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="c1d29-139">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-139">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="c1d29-140">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-140">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="c1d29-141">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-141">Update the markup to indicate the new location.</span></span> <span data-ttu-id="c1d29-142">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-142">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="c1d29-143">RCL ページ レイアウト</span><span class="sxs-lookup"><span data-stu-id="c1d29-143">RCL Pages layout</span></span>

<span data-ttu-id="c1d29-144">参照 RCL コンテンツの web アプリの一部である場合と同様に*ページ*フォルダー、RCL プロジェクト ファイルの構造を作成します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-144">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="c1d29-145">*RazorUIClassLib/ページ*</span><span class="sxs-lookup"><span data-stu-id="c1d29-145">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="c1d29-146">*RazorUIClassLib/ページ/共有*</span><span class="sxs-lookup"><span data-stu-id="c1d29-146">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="c1d29-147">たとえば*RazorUIClassLib/ページ/共有*2 つの部分的なファイルが含まれています: *_Header.cshtml*と *_Footer.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-147">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="c1d29-148">`<partial>`タグに追加できる *_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="c1d29-148">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="c1d29-149">静的なアセットを含む RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="c1d29-149">Create an RCL with static assets</span></span>

<span data-ttu-id="c1d29-150">RCL では、RCL の消費アプリから参照できる、関連する静的アセットが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-150">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="c1d29-151">ASP.NET Core では、使用中のアプリで利用可能な静的アセットを含む RCLs を作成できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-151">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="c1d29-152">RCL の一部としてコンパニオン資産を含めるには、クラスライブラリに*wwwroot*フォルダーを作成し、そのフォルダーに必要なファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-152">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="c1d29-153">RCL をパッキングすると、 *wwwroot*フォルダー内のすべてのコンパニオン資産がパッケージに自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-153">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="c1d29-154">静的アセットを除外する</span><span class="sxs-lookup"><span data-stu-id="c1d29-154">Exclude static assets</span></span>

<span data-ttu-id="c1d29-155">静的アセットを除外するには、目的の除外パスをプロジェクトファイルの `$(DefaultItemExcludes)` プロパティグループに追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-155">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="c1d29-156">エントリをセミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-156">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="c1d29-157">次の例では、 *wwwroot*フォルダーの *.lib*スタイルシートは静的アセットとは見なされず、公開された rcl には含まれていません。</span><span class="sxs-lookup"><span data-stu-id="c1d29-157">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="c1d29-158">Typescript の統合</span><span class="sxs-lookup"><span data-stu-id="c1d29-158">Typescript integration</span></span>

<span data-ttu-id="c1d29-159">TypeScript ファイルを RCL に含めるには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-159">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="c1d29-160">TypeScript ファイル (*ts*) を*wwwroot*フォルダーの外側に配置します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-160">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="c1d29-161">たとえば、ファイルを*クライアント*フォルダーに配置します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-161">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="c1d29-162">*Wwwroot*フォルダーの TypeScript ビルド出力を構成します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-162">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="c1d29-163">プロジェクトファイルの `PropertyGroup` 内の `TypescriptOutDir` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-163">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="c1d29-164">@No__t-0 ターゲットの依存関係として TypeScript ターゲットを追加します。そのためには、プロジェクトファイルの `PropertyGroup` 内に次のターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-164">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="c1d29-165">参照されている RCL からのコンテンツの使用</span><span class="sxs-lookup"><span data-stu-id="c1d29-165">Consume content from a referenced RCL</span></span>

<span data-ttu-id="c1d29-166">RCL の*wwwroot*フォルダーに含まれるファイルは、消費側のアプリのプレフィックス `_content/{LIBRARY NAME}/` で公開されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-166">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="c1d29-167">たとえば、という名前のライブラリを使用*すると、* `_content/Razor.Class.Lib/` の静的コンテンツへのパスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-167">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="c1d29-168">使用中のアプリは、`<script>`、`<style>`、`<img>`、およびその他の HTML タグを持つライブラリによって提供される静的アセットを参照します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-168">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="c1d29-169">コンシューマーアプリでは、`Startup.Configure` で[静的ファイルサポート](xref:fundamentals/static-files)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-169">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="c1d29-170">ビルド出力 (`dotnet run`) から使用中のアプリを実行すると、開発環境では、静的な web アセットが既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-170">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="c1d29-171">ビルド出力から実行するときに他の環境のアセットをサポートするには、 *Program.cs*のホストビルダーで `UseStaticWebAssets` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-171">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="c1d29-172">発行された出力からアプリを実行する場合は、`UseStaticWebAssets` を呼び出す必要はありません (`dotnet publish`)。</span><span class="sxs-lookup"><span data-stu-id="c1d29-172">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="c1d29-173">複数プロジェクトの開発フロー</span><span class="sxs-lookup"><span data-stu-id="c1d29-173">Multi-project development flow</span></span>

<span data-ttu-id="c1d29-174">コンシューマーアプリの実行時:</span><span class="sxs-lookup"><span data-stu-id="c1d29-174">When the consuming app runs:</span></span>

* <span data-ttu-id="c1d29-175">RCL のアセットは元のフォルダーにとどまります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-175">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="c1d29-176">アセットは、消費側のアプリに移動されません。</span><span class="sxs-lookup"><span data-stu-id="c1d29-176">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="c1d29-177">Rcl の*wwwroot*フォルダー内の変更は、rcl がリビルドされた後、使用中のアプリを再構築せずに、消費側アプリに反映されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-177">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="c1d29-178">RCL がビルドされると、静的な web 資産の場所を記述するマニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-178">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="c1d29-179">コンシューマーアプリは、実行時にマニフェストを読み取り、参照されたプロジェクトとパッケージのアセットを使用します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-179">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="c1d29-180">RCL に新しいアセットが追加されたときに、使用中のアプリが新しい資産にアクセスできるようにするには、そのマニフェストを更新するために RCL を再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-180">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="c1d29-181">パブリッシュ</span><span class="sxs-lookup"><span data-stu-id="c1d29-181">Publish</span></span>

<span data-ttu-id="c1d29-182">アプリが発行されると、参照されているすべてのプロジェクトおよびパッケージのコンパニオン資産が、`_content/{LIBRARY NAME}/` の下で、発行されたアプリの*wwwroot*フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-182">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1d29-183">Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-183">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="c1d29-184">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-184">The RCL can be packaged and reused.</span></span> <span data-ttu-id="c1d29-185">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-185">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="c1d29-186">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-186">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="c1d29-187">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c1d29-187">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="c1d29-188">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="c1d29-188">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1d29-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d29-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1d29-190">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c1d29-191">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-191">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c1d29-192">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-192">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="c1d29-193">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-193">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="c1d29-194">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-194">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="c1d29-195">[ **Razor クラスライブラリ**> **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-195">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="c1d29-196">RCL には、次のプロジェクトファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="c1d29-196">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c1d29-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c1d29-197">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c1d29-198">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-198">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="c1d29-199">例:</span><span class="sxs-lookup"><span data-stu-id="c1d29-199">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="c1d29-200">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-200">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="c1d29-201">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-201">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="c1d29-202">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-202">Add Razor files to the RCL.</span></span>

<span data-ttu-id="c1d29-203">ASP.NET Core テンプレートは、RCL コンテンツが前提としています。、*領域*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="c1d29-203">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="c1d29-204">@No__t ではなく `~/Pages` でコンテンツを公開する RCL を作成するには、「 [Rcl ページレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-204">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="c1d29-205">RCL コンテンツの参照</span><span class="sxs-lookup"><span data-stu-id="c1d29-205">Reference RCL content</span></span>

<span data-ttu-id="c1d29-206">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-206">The RCL can be referenced by:</span></span>

* <span data-ttu-id="c1d29-207">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="c1d29-207">NuGet package.</span></span> <span data-ttu-id="c1d29-208">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-208">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="c1d29-209">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c1d29-209">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="c1d29-210">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-210">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="c1d29-211">チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する</span><span class="sxs-lookup"><span data-stu-id="c1d29-211">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="c1d29-212">作成しなくても、[完全なプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-212">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="c1d29-213">サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c1d29-213">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="c1d29-214">GitHub の問題に関するフィードバックは[こちら](https://github.com/aspnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-214">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="c1d29-215">ダウンロード アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c1d29-215">Test the download app</span></span>

<span data-ttu-id="c1d29-216">完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-216">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1d29-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d29-217">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1d29-218">Visual Studio で *.sln* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-218">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="c1d29-219">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-219">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c1d29-220">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c1d29-220">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c1d29-221">*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-221">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="c1d29-222">*WebApp1* ディレクトリに移動し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-222">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="c1d29-223">[テスト WebApp1](#test-webapp1) の指示に従ってください。</span><span class="sxs-lookup"><span data-stu-id="c1d29-223">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="c1d29-224">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="c1d29-224">Create an RCL</span></span>

<span data-ttu-id="c1d29-225">このセクションでは、RCL を作成します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-225">In this section, an RCL is created.</span></span> <span data-ttu-id="c1d29-226">Razor ファイルが RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-226">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1d29-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d29-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1d29-228">RCL プロジェクトの作成:</span><span class="sxs-lookup"><span data-stu-id="c1d29-228">Create the RCL project:</span></span>

* <span data-ttu-id="c1d29-229">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-229">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c1d29-230">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-230">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c1d29-231">アプリに**RazorUIClassLib**という名前を > **OK をクリック**します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-231">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="c1d29-232">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-232">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="c1d29-233">[ **Razor クラスライブラリ**> **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-233">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="c1d29-234">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-234">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c1d29-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c1d29-235">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c1d29-236">コマンド ラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-236">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="c1d29-237">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-237">The preceding commands:</span></span>

* <span data-ttu-id="c1d29-238">@No__t-0 RCL を作成します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-238">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="c1d29-239">Razor _Message ページが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-239">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="c1d29-240">`-np` パラメーターによって、`PageModel` なしでページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-240">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="c1d29-241">作成、 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)ファイルを開き、RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-241">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="c1d29-242">*_ViewStart.cshtml* (これは、次のセクションに追加されます) Razor ページ プロジェクトのレイアウトを使用するファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="c1d29-242">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="c1d29-243">Razor ファイルおよびフォルダーをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-243">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="c1d29-244">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-244">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="c1d29-245">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="c1d29-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="c1d29-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="c1d29-247">`@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-247">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c1d29-248">例:</span><span class="sxs-lookup"><span data-stu-id="c1d29-248">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="c1d29-249">詳細については *_ViewImports.cshtml*を参照してください[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="c1d29-249">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="c1d29-250">クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-250">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="c1d29-251">ビルド出力には、*RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c1d29-251">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="c1d29-252">*RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c1d29-252">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="c1d29-253">Razor ページ プロジェクトから Razor UI ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-253">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1d29-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d29-254">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1d29-255">Razor ページ Web アプリの作成:</span><span class="sxs-lookup"><span data-stu-id="c1d29-255">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="c1d29-256">**ソリューションエクスプローラー**から、ソリューションを右クリックして > >**新しいプロジェクト**を**追加**します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-256">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="c1d29-257">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-257">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c1d29-258">アプリに **WebApp1** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-258">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="c1d29-259">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-259">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="c1d29-260">[ **Web アプリケーション**> **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-260">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="c1d29-261">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-261">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="c1d29-262">**ソリューションエクスプローラー**で、 **[WebApp1]** を右クリックし、 **[ビルドの依存]** 関係 を選択し @no__t**プロジェクトの依存関係**を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-262">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="c1d29-263">**WebApp1** の依存関係として **RazorUIClassLib** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-263">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="c1d29-264">**ソリューションエクスプローラー**で、 **[WebApp1]** を右クリックし、@no__t の **[追加]** を選択**します。**</span><span class="sxs-lookup"><span data-stu-id="c1d29-264">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="c1d29-265">**[参照マネージャー]** ダイアログで、[ **RazorUIClassLib** > **OK]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-265">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="c1d29-266">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-266">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c1d29-267">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c1d29-267">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c1d29-268">Razor Pages web アプリと、Razor Pages アプリと RCL を含むソリューションファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-268">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="c1d29-269">Web アプリをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-269">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="c1d29-270">テスト WebApp1</span><span class="sxs-lookup"><span data-stu-id="c1d29-270">Test WebApp1</span></span>

<span data-ttu-id="c1d29-271">@No__t-0 を参照して、Razor UI クラスライブラリが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-271">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="c1d29-272">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="c1d29-272">Override views, partial views, and pages</span></span>

<span data-ttu-id="c1d29-273">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-273">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="c1d29-274">たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="c1d29-274">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="c1d29-275">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-275">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="c1d29-276">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="c1d29-276">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="c1d29-277">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-277">Update the markup to indicate the new location.</span></span> <span data-ttu-id="c1d29-278">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-278">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="c1d29-279">RCL ページ レイアウト</span><span class="sxs-lookup"><span data-stu-id="c1d29-279">RCL Pages layout</span></span>

<span data-ttu-id="c1d29-280">参照 RCL コンテンツの web アプリの一部である場合と同様に*ページ*フォルダー、RCL プロジェクト ファイルの構造を作成します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-280">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="c1d29-281">*RazorUIClassLib/ページ*</span><span class="sxs-lookup"><span data-stu-id="c1d29-281">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="c1d29-282">*RazorUIClassLib/ページ/共有*</span><span class="sxs-lookup"><span data-stu-id="c1d29-282">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="c1d29-283">たとえば*RazorUIClassLib/ページ/共有*2 つの部分的なファイルが含まれています: *_Header.cshtml*と *_Footer.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="c1d29-283">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="c1d29-284">`<partial>`タグに追加できる *_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="c1d29-284">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
