---
title: ASP.NET Core での静的資産のバンドルと縮小
author: scottaddie
description: バンドルと縮小の手法を適用して ASP.NET Core web アプリケーションで静的リソースを最適化する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7499381a24a2513a4fbd1205f245e624c86647c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080559"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="563e1-103">ASP.NET Core での静的資産のバンドルと縮小</span><span class="sxs-lookup"><span data-stu-id="563e1-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="563e1-104">[Scott Addie](https://twitter.com/Scott_Addie)と[David 松](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="563e1-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="563e1-105">この記事では、ASP.NET Core web アプリでこれらの機能を使用する方法など、バンドルと縮小を適用する利点について説明します。</span><span class="sxs-lookup"><span data-stu-id="563e1-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="563e1-106">バンドルと縮小とは</span><span class="sxs-lookup"><span data-stu-id="563e1-106">What is bundling and minification</span></span>

<span data-ttu-id="563e1-107">バンドルと縮小は、web アプリで適用できる2つの異なるパフォーマンス最適化です。</span><span class="sxs-lookup"><span data-stu-id="563e1-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="563e1-108">サーバー要求の数を減らし、要求された静的な資産のサイズを小さくすることで、バンドルと縮小を一緒に使用してパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="563e1-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="563e1-109">バンドルと縮小は、主に最初のページ要求の読み込み時間を短縮します。</span><span class="sxs-lookup"><span data-stu-id="563e1-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="563e1-110">Web ページが要求されると、ブラウザーは静的なアセット (JavaScript、CSS、およびイメージ) をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="563e1-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="563e1-111">そのため、同じサイトで同じ資産を要求している同じページまたはページを要求した場合、バンドルと縮小によってパフォーマンスが向上することはありません。</span><span class="sxs-lookup"><span data-stu-id="563e1-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="563e1-112">有効期限ヘッダーがアセットに対して正しく設定されていない場合、バンドルと縮小が使用されていない場合、ブラウザーの鮮度ヒューリスティックによって、数日後に古い資産が古くなっているとマークされます。</span><span class="sxs-lookup"><span data-stu-id="563e1-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="563e1-113">さらに、ブラウザーでは、各資産に対する検証要求が必要です。</span><span class="sxs-lookup"><span data-stu-id="563e1-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="563e1-114">この場合、バンドルと縮小によって、最初のページ要求の後でもパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="563e1-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="563e1-115">まとめる</span><span class="sxs-lookup"><span data-stu-id="563e1-115">Bundling</span></span>

<span data-ttu-id="563e1-116">バンドルは、複数のファイルを1つのファイルに結合します。</span><span class="sxs-lookup"><span data-stu-id="563e1-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="563e1-117">バンドルを行うと、web ページなどの web アセットをレンダリングするために必要なサーバー要求の数が減ります。</span><span class="sxs-lookup"><span data-stu-id="563e1-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="563e1-118">CSS や JavaScript などに対して、任意の数の個別バンドルを作成できます。ファイルが減るほど、ブラウザーからサーバーまたはアプリケーションを提供するサービスへの HTTP 要求が減少します。</span><span class="sxs-lookup"><span data-stu-id="563e1-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="563e1-119">その結果、最初のページ読み込みのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="563e1-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="563e1-120">縮小</span><span class="sxs-lookup"><span data-stu-id="563e1-120">Minification</span></span>

<span data-ttu-id="563e1-121">縮小機能を変更せずに、コードから不要な文字を削除します。</span><span class="sxs-lookup"><span data-stu-id="563e1-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="563e1-122">結果として、要求された資産 (CSS、画像、JavaScript ファイルなど) のサイズが大幅に削減されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="563e1-123">縮小の一般的な副作用には、変数名を1文字に短縮し、コメントや不要な空白を削除することがあります。</span><span class="sxs-lookup"><span data-stu-id="563e1-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="563e1-124">次の JavaScript 関数を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="563e1-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="563e1-125">縮小により、関数が次のように短縮されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="563e1-126">コメントや不要な空白文字を削除するだけでなく、次のパラメーター名と変数名が次のように変更されました。</span><span class="sxs-lookup"><span data-stu-id="563e1-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="563e1-127">元</span><span class="sxs-lookup"><span data-stu-id="563e1-127">Original</span></span> | <span data-ttu-id="563e1-128">名前の変更</span><span class="sxs-lookup"><span data-stu-id="563e1-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="563e1-129">バンドルと縮小の影響</span><span class="sxs-lookup"><span data-stu-id="563e1-129">Impact of bundling and minification</span></span>

<span data-ttu-id="563e1-130">次の表は、アセットを個別に読み込んで、バンドルと縮小を使用する場合の違いをまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="563e1-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="563e1-131">アクション</span><span class="sxs-lookup"><span data-stu-id="563e1-131">Action</span></span> | <span data-ttu-id="563e1-132">B/M を使用</span><span class="sxs-lookup"><span data-stu-id="563e1-132">With B/M</span></span> | <span data-ttu-id="563e1-133">B/M なし</span><span class="sxs-lookup"><span data-stu-id="563e1-133">Without B/M</span></span> | <span data-ttu-id="563e1-134">[Change]</span><span class="sxs-lookup"><span data-stu-id="563e1-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="563e1-135">ファイル要求</span><span class="sxs-lookup"><span data-stu-id="563e1-135">File Requests</span></span>  | <span data-ttu-id="563e1-136">7</span><span class="sxs-lookup"><span data-stu-id="563e1-136">7</span></span>   | <span data-ttu-id="563e1-137">18</span><span class="sxs-lookup"><span data-stu-id="563e1-137">18</span></span>     | <span data-ttu-id="563e1-138">157%</span><span class="sxs-lookup"><span data-stu-id="563e1-138">157%</span></span>
<span data-ttu-id="563e1-139">転送された KB</span><span class="sxs-lookup"><span data-stu-id="563e1-139">KB Transferred</span></span> | <span data-ttu-id="563e1-140">156</span><span class="sxs-lookup"><span data-stu-id="563e1-140">156</span></span> | <span data-ttu-id="563e1-141">264.68</span><span class="sxs-lookup"><span data-stu-id="563e1-141">264.68</span></span> | <span data-ttu-id="563e1-142">70%</span><span class="sxs-lookup"><span data-stu-id="563e1-142">70%</span></span>
<span data-ttu-id="563e1-143">読み込み時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563e1-143">Load Time (ms)</span></span> | <span data-ttu-id="563e1-144">885</span><span class="sxs-lookup"><span data-stu-id="563e1-144">885</span></span> | <span data-ttu-id="563e1-145">2360</span><span class="sxs-lookup"><span data-stu-id="563e1-145">2360</span></span>   | <span data-ttu-id="563e1-146">167%</span><span class="sxs-lookup"><span data-stu-id="563e1-146">167%</span></span>

<span data-ttu-id="563e1-147">ブラウザーは、HTTP 要求ヘッダーに関してかなり冗長です。</span><span class="sxs-lookup"><span data-stu-id="563e1-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="563e1-148">送信された合計バイト数のメトリックは、バンドルするときに大幅に削減されていました。</span><span class="sxs-lookup"><span data-stu-id="563e1-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="563e1-149">読み込み時間は大幅に改善されていますが、この例はローカルで実行されています。</span><span class="sxs-lookup"><span data-stu-id="563e1-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="563e1-150">ネットワーク経由で転送される資産でバンドルと縮小を使用すると、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="563e1-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="563e1-151">バンドルと縮小の戦略を選択する</span><span class="sxs-lookup"><span data-stu-id="563e1-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="563e1-152">MVC と Razor Pages のプロジェクトテンプレートには、JSON 構成ファイルで構成されるバンドルと縮小のためのすぐに使用できるソリューションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="563e1-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="563e1-153">[Grunt](xref:client-side/using-grunt)タスクランナーなどのサードパーティのツールでは、同じタスクを少し複雑にして実行します。</span><span class="sxs-lookup"><span data-stu-id="563e1-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="563e1-154">サードパーティ製のツールは、開発ワークフローが、ラインリングやイメージの最適化などの&mdash;バンドルと縮小を超える処理を必要とする場合に、非常に適しています。</span><span class="sxs-lookup"><span data-stu-id="563e1-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="563e1-155">デザイン時のバンドルと縮小を使用することにより、アプリの展開の前に縮小したファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="563e1-156">配置前のバンドルと縮小によって、サーバーの負荷が軽減されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="563e1-157">ただし、デザイン時のバンドルと縮小がビルドの複雑さを増し、静的ファイルでのみ機能することを認識することが重要です。</span><span class="sxs-lookup"><span data-stu-id="563e1-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="563e1-158">バンドルと縮小の構成</span><span class="sxs-lookup"><span data-stu-id="563e1-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="563e1-159">ASP.NET Core 2.0 以前では、MVC および Razor Pages のプロジェクトテンプレートには、各バンドルのオプションを定義する bundleconfig 構成ファイルが用意されて*い*ます。</span><span class="sxs-lookup"><span data-stu-id="563e1-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="563e1-160">ASP.NET Core 2.1 以降では、MVC または Razor Pages プロジェクトルートに*bundleconfig*という名前の新しい json ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="563e1-161">次の JSON を開始点としてそのファイルに含めます。</span><span class="sxs-lookup"><span data-stu-id="563e1-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="563e1-162">Bundleconfig ファイルは、各バンドルのオプションを定義し*ます*。</span><span class="sxs-lookup"><span data-stu-id="563e1-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="563e1-163">前の例では、カスタム JavaScript (*wwwroot/js/* node.js) と stylesheet (*wwwroot/css/.css*) ファイルに対して1つのバンドル構成が定義されています。</span><span class="sxs-lookup"><span data-stu-id="563e1-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="563e1-164">構成オプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="563e1-164">Configuration options include:</span></span>

* <span data-ttu-id="563e1-165">`outputFileName`:出力するバンドルファイルの名前。</span><span class="sxs-lookup"><span data-stu-id="563e1-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="563e1-166">には、 *bundleconfig*ファイルからの相対パスを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="563e1-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="563e1-167">**必須**</span><span class="sxs-lookup"><span data-stu-id="563e1-167">**required**</span></span>
* <span data-ttu-id="563e1-168">`inputFiles`:まとめてバンドルするファイルの配列。</span><span class="sxs-lookup"><span data-stu-id="563e1-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="563e1-169">これらは、構成ファイルへの相対パスです。</span><span class="sxs-lookup"><span data-stu-id="563e1-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="563e1-170">**省略可能**、\* 空の値を指定すると、空の出力ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="563e1-171">[グロビング](https://www.tldp.org/LDP/abs/html/globbingref.html)パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="563e1-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="563e1-172">`minify`:出力の種類の縮小オプション。</span><span class="sxs-lookup"><span data-stu-id="563e1-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="563e1-173">**省略可能**、*既定`minify: { enabled: true }`値-*</span><span class="sxs-lookup"><span data-stu-id="563e1-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="563e1-174">構成オプションは、出力ファイルの種類ごとに使用できます。</span><span class="sxs-lookup"><span data-stu-id="563e1-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="563e1-175">CSS ミニ識別子</span><span class="sxs-lookup"><span data-stu-id="563e1-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="563e1-176">JavaScript ミニ識別子</span><span class="sxs-lookup"><span data-stu-id="563e1-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="563e1-177">HTML ミニ識別子</span><span class="sxs-lookup"><span data-stu-id="563e1-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="563e1-178">`includeInProject`:生成されたファイルをプロジェクトファイルに追加するかどうかを示すフラグです。</span><span class="sxs-lookup"><span data-stu-id="563e1-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="563e1-179">**省略可能**、*既定値-false*</span><span class="sxs-lookup"><span data-stu-id="563e1-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="563e1-180">`sourceMap` :バンドルされたファイルのソースマップを生成するかどうかを示すフラグです。</span><span class="sxs-lookup"><span data-stu-id="563e1-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="563e1-181">**省略可能**、*既定値-false*</span><span class="sxs-lookup"><span data-stu-id="563e1-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="563e1-182">`sourceMapRootPath`:生成されたソースマップファイルを格納するためのルートパス。</span><span class="sxs-lookup"><span data-stu-id="563e1-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="563e1-183">バンドルと縮小のビルド時の実行</span><span class="sxs-lookup"><span data-stu-id="563e1-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="563e1-184">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet パッケージを使用すると、ビルド時にバンドルと縮小を実行できます。</span><span class="sxs-lookup"><span data-stu-id="563e1-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="563e1-185">パッケージは、ビルド時とクリーンアップ時に実行される[MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)を挿入します。</span><span class="sxs-lookup"><span data-stu-id="563e1-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="563e1-186">*Bundleconfig*ファイルは、定義された構成に基づいて出力ファイルを生成するために、ビルドプロセスによって分析されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="563e1-187">BuildBundlerMinifier は、Microsoft がサポートしていない GitHub のコミュニティ主導のプロジェクトに属しています。</span><span class="sxs-lookup"><span data-stu-id="563e1-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="563e1-188">[ここで](https://github.com/madskristensen/BundlerMinifier/issues)問題を提出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="563e1-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="563e1-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="563e1-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="563e1-190">*BuildBundlerMinifier*パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="563e1-191">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="563e1-191">Build the project.</span></span> <span data-ttu-id="563e1-192">[出力] ウィンドウに次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="563e1-193">プロジェクトを消去します。</span><span class="sxs-lookup"><span data-stu-id="563e1-193">Clean the project.</span></span> <span data-ttu-id="563e1-194">[出力] ウィンドウに次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="563e1-195">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="563e1-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="563e1-196">*BuildBundlerMinifier*パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="563e1-197">ASP.NET Core 1.x を使用している場合は、新しく追加したパッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="563e1-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="563e1-198">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="563e1-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="563e1-199">次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="563e1-200">プロジェクトを消去します。</span><span class="sxs-lookup"><span data-stu-id="563e1-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="563e1-201">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="563e1-202">バンドルと縮小のアドホック実行</span><span class="sxs-lookup"><span data-stu-id="563e1-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="563e1-203">プロジェクトをビルドしなくても、バンドルタスクと縮小タスクをアドホックベースで実行できます。</span><span class="sxs-lookup"><span data-stu-id="563e1-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="563e1-204">[BundlerMinifier](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="563e1-205">BundlerMinifier は、Microsoft がサポートしていない GitHub のコミュニティ主導のプロジェクトに属しています。</span><span class="sxs-lookup"><span data-stu-id="563e1-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="563e1-206">[ここで](https://github.com/madskristensen/BundlerMinifier/issues)問題を提出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="563e1-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="563e1-207">このパッケージは、 *dotnet*ツールを含むように .NET Core CLI を拡張します。</span><span class="sxs-lookup"><span data-stu-id="563e1-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="563e1-208">次のコマンドは、パッケージマネージャーコンソール (PMC) ウィンドウまたはコマンドシェルで実行できます。</span><span class="sxs-lookup"><span data-stu-id="563e1-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="563e1-209">NuGet パッケージマネージャーは、\* .csproj ファイルにノードとし`<PackageReference />`て依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="563e1-210">コマンドは、 `<DotNetCliToolReference />`ノードが使用されている場合にのみ .NET Core CLI に登録されます。 `dotnet bundle`</span><span class="sxs-lookup"><span data-stu-id="563e1-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="563e1-211">必要に応じて \* .csproj ファイルを変更します。</span><span class="sxs-lookup"><span data-stu-id="563e1-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="563e1-212">ワークフローへのファイルの追加</span><span class="sxs-lookup"><span data-stu-id="563e1-212">Add files to workflow</span></span>

<span data-ttu-id="563e1-213">次のように、追加の*カスタム .css*ファイルが追加される例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="563e1-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="563e1-214">*カスタム .css*を縮小して、*サイト* *の .css ファイルに*バンドルするには、 *bundleconfig*への相対パスを追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="563e1-215">または、次のグロビングパターンを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="563e1-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> <span data-ttu-id="563e1-216">このグロビングパターンは、すべての CSS ファイルに一致し、縮小されたファイルパターンは除外されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="563e1-217">アプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="563e1-217">Build the application.</span></span> <span data-ttu-id="563e1-218">[ *.Css* ] を開き、[*カスタム .css* ] の内容がファイルの末尾に追加されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="563e1-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="563e1-219">環境ベースのバンドルと縮小</span><span class="sxs-lookup"><span data-stu-id="563e1-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="563e1-220">ベストプラクティスとして、アプリのバンドルファイルと縮小版ファイルを運用環境で使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="563e1-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="563e1-221">開発時には、元のファイルによってアプリのデバッグが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="563e1-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="563e1-222">ビューで[環境タグヘルパー](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)を使用して、ページに含めるファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="563e1-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="563e1-223">環境タグヘルパーは、特定の[環境](xref:fundamentals/environments)で実行されている場合にのみコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="563e1-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="563e1-224">次`environment`のタグは、 `Development`環境で実行されているときに、未処理の CSS ファイルをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="563e1-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="563e1-225">次`environment`のタグは、以外`Development`の環境で実行されている場合に、バンドルされた、縮小された CSS ファイルをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="563e1-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="563e1-226">たとえば、または`Production` `Staging`でを実行すると、次のスタイルシートのレンダリングがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="563e1-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="563e1-227">Gulp から bundleconfig を使用する</span><span class="sxs-lookup"><span data-stu-id="563e1-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="563e1-228">アプリのバンドルと縮小ワークフローで追加の処理が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="563e1-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="563e1-229">例としては、イメージの最適化、キャッシュ処理、CDN 資産の処理などがあります。</span><span class="sxs-lookup"><span data-stu-id="563e1-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="563e1-230">これらの要件を満たすために、Gulp を使用するようにバンドルと縮小のワークフローを変換できます。</span><span class="sxs-lookup"><span data-stu-id="563e1-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="563e1-231">Bundler & Minifier 拡張機能を使用する</span><span class="sxs-lookup"><span data-stu-id="563e1-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="563e1-232">Visual Studio [Bundler の & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)拡張機能は、への変換を処理します。</span><span class="sxs-lookup"><span data-stu-id="563e1-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="563e1-233">Bundler & Minifier 拡張機能は、Microsoft がサポートしていない GitHub のコミュニティ主導のプロジェクトに属しています。</span><span class="sxs-lookup"><span data-stu-id="563e1-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="563e1-234">[ここで](https://github.com/madskristensen/BundlerMinifier/issues)問題を提出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="563e1-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="563e1-235">ソリューションエクスプローラーで*bundleconfig*ファイルを右クリックし、[ **Bundler &**  > minifier**Convert To Gulp...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="563e1-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Gulp コンテキストメニュー項目に変換する](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="563e1-237">*Gulpfile*ファイルと*パッケージ*ファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="563e1-238">*パッケージの json*ファイルの`devDependencies`セクションに一覧表示されている[npm](https://www.npmjs.com/)パッケージのサポートがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="563e1-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="563e1-239">PMC ウィンドウで次のコマンドを実行して、Gulp CLI をグローバルな依存関係としてインストールします。</span><span class="sxs-lookup"><span data-stu-id="563e1-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="563e1-240">*Gulpfile*ファイルは、入力、出力、および設定の*bundleconfig*ファイルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="563e1-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="563e1-241">手動で変換</span><span class="sxs-lookup"><span data-stu-id="563e1-241">Convert manually</span></span>

<span data-ttu-id="563e1-242">Visual Studio または Bundler & Minifier の拡張機能が使用できない場合は、手動で変換します。</span><span class="sxs-lookup"><span data-stu-id="563e1-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="563e1-243">次`devDependencies`のを含む*パッケージの json*ファイルをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="563e1-244">モジュール`gulp-uglify`は、ECMAScript (ES) 2015/ES6 以降をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="563e1-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="563e1-245">ES2015/ES6 以降を使用する`gulp-uglify`のではなく[、gulp を](https://www.npmjs.com/package/gulp-terser)インストールします。</span><span class="sxs-lookup"><span data-stu-id="563e1-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="563e1-246">次のコマンドを*package. json*と同じレベルで実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="563e1-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="563e1-247">グローバルな依存関係として Gulp CLI をインストールします。</span><span class="sxs-lookup"><span data-stu-id="563e1-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="563e1-248">次の*gulpfile*ファイルをプロジェクトのルートにコピーします。</span><span class="sxs-lookup"><span data-stu-id="563e1-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="563e1-249">Gulp タスクの実行</span><span class="sxs-lookup"><span data-stu-id="563e1-249">Run Gulp tasks</span></span>

<span data-ttu-id="563e1-250">Visual Studio でプロジェクトをビルドする前に Gulp の縮小タスクをトリガーするには、次の[MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)を \* .csproj ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="563e1-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="563e1-251">この例では、 `MyPreCompileTarget`ターゲット内で定義されているすべてのタスクが、定義済み`Build`のターゲットの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="563e1-252">次のような出力が Visual Studio の出力ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="563e1-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```


## <a name="additional-resources"></a><span data-ttu-id="563e1-253">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="563e1-253">Additional resources</span></span>

* [<span data-ttu-id="563e1-254">Grunt の使用</span><span class="sxs-lookup"><span data-stu-id="563e1-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="563e1-255">複数の環境の使用</span><span class="sxs-lookup"><span data-stu-id="563e1-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="563e1-256">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="563e1-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
