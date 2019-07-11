---
title: バンドルし、縮小の ASP.NET Core で静的なアセット
author: scottaddie
description: バンドルと縮小の手法を適用することで、ASP.NET Core web アプリケーションで静的なリソースを最適化する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: 6254a74fd0a11669706a2a89b156a3223e300d1c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813506"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="bcf7a-103">バンドルし、縮小の ASP.NET Core で静的なアセット</span><span class="sxs-lookup"><span data-stu-id="bcf7a-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="bcf7a-104">によって[Scott Addie](https://twitter.com/Scott_Addie)と[David 松](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="bcf7a-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="bcf7a-105">この記事では、バンドルや縮小、これらの機能を使用して ASP.NET Core web アプリの使用方法などを適用する利点について説明します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="bcf7a-106">新機能のバンドルと縮小</span><span class="sxs-lookup"><span data-stu-id="bcf7a-106">What is bundling and minification</span></span>

<span data-ttu-id="bcf7a-107">バンドルと縮小は、web アプリに適用できる 2 つの個別のパフォーマンス最適化です。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="bcf7a-108">を一緒に使用バンドルと縮小パフォーマンス向上のサーバー要求の数を減らすと、要求された静的なアセットのサイズを小さきます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="bcf7a-109">バンドルと縮小は、最初のページ要求の読み込み時間を向上させる主にします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="bcf7a-110">Web ページを要求されたら、ブラウザーは、静的なアセット (JavaScript、CSS、およびイメージ) をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="bcf7a-111">その結果、バンドルと縮小しないパフォーマンスを向上させる、同じページで、または同じ資産を要求するのと同じサイト上のページを要求するときにします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="bcf7a-112">場合、有効期限が切れる資産にヘッダーが正しく設定されていないし、バンドルと縮小が使用されていない場合、ブラウザーの鮮度ヒューリスティック マーク資産古い数日後。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="bcf7a-113">さらに、ブラウザーでは、各資産の検証要求が必要です。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="bcf7a-114">この場合は、バンドルと縮小は、最初のページ要求した後でもパフォーマンスの向上を提供します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="bcf7a-115">バンドル</span><span class="sxs-lookup"><span data-stu-id="bcf7a-115">Bundling</span></span>

<span data-ttu-id="bcf7a-116">1 つのファイルに複数のファイルを結合バンドルします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="bcf7a-117">バンドルは、web ページなどの web アセットを表示するために必要なサーバー要求の数を減らします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="bcf7a-118">CSS、JavaScript などの具体的には、任意の数の個別のバンドルを作成できます。少ないファイルでは、サーバーにブラウザーまたはアプリケーションを提供するサービスから HTTP の要求が減少を意味します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="bcf7a-119">この結果には、最初のページ読み込みのパフォーマンスが向上しました。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="bcf7a-120">縮小</span><span class="sxs-lookup"><span data-stu-id="bcf7a-120">Minification</span></span>

<span data-ttu-id="bcf7a-121">縮小では、機能を変更することがなく、コードから不要な文字を削除します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="bcf7a-122">要求されたアセット (CSS、画像、JavaScript ファイルなど) に大きなサイズの削減になります。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="bcf7a-123">縮小の一般的な副作用は、1 つの文字を変数名を短く、コメントや不要な空白文字を削除するなどがあります。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="bcf7a-124">次の JavaScript 関数を検討してください。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="bcf7a-125">縮小では、次に関数を削減します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="bcf7a-126">だけでなく、コメントと不要な空白文字を削除するには、次のパラメーターと変数名が名前を変更されました。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="bcf7a-127">元</span><span class="sxs-lookup"><span data-stu-id="bcf7a-127">Original</span></span> | <span data-ttu-id="bcf7a-128">名前の変更</span><span class="sxs-lookup"><span data-stu-id="bcf7a-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="bcf7a-129">バンドルと縮小の影響</span><span class="sxs-lookup"><span data-stu-id="bcf7a-129">Impact of bundling and minification</span></span>

<span data-ttu-id="bcf7a-130">次の表では、個別に資産の読み込みとバンドルと縮小を使用して違いを示します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="bcf7a-131">アクション</span><span class="sxs-lookup"><span data-stu-id="bcf7a-131">Action</span></span> | <span data-ttu-id="bcf7a-132">B/m</span><span class="sxs-lookup"><span data-stu-id="bcf7a-132">With B/M</span></span> | <span data-ttu-id="bcf7a-133">B/分なし</span><span class="sxs-lookup"><span data-stu-id="bcf7a-133">Without B/M</span></span> | <span data-ttu-id="bcf7a-134">[Change]</span><span class="sxs-lookup"><span data-stu-id="bcf7a-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="bcf7a-135">ファイルの要求</span><span class="sxs-lookup"><span data-stu-id="bcf7a-135">File Requests</span></span>  | <span data-ttu-id="bcf7a-136">7</span><span class="sxs-lookup"><span data-stu-id="bcf7a-136">7</span></span>   | <span data-ttu-id="bcf7a-137">18</span><span class="sxs-lookup"><span data-stu-id="bcf7a-137">18</span></span>     | <span data-ttu-id="bcf7a-138">157%</span><span class="sxs-lookup"><span data-stu-id="bcf7a-138">157%</span></span>
<span data-ttu-id="bcf7a-139">サポート技術情報の転送</span><span class="sxs-lookup"><span data-stu-id="bcf7a-139">KB Transferred</span></span> | <span data-ttu-id="bcf7a-140">156</span><span class="sxs-lookup"><span data-stu-id="bcf7a-140">156</span></span> | <span data-ttu-id="bcf7a-141">264.68</span><span class="sxs-lookup"><span data-stu-id="bcf7a-141">264.68</span></span> | <span data-ttu-id="bcf7a-142">70%</span><span class="sxs-lookup"><span data-stu-id="bcf7a-142">70%</span></span>
<span data-ttu-id="bcf7a-143">読み込み時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="bcf7a-143">Load Time (ms)</span></span> | <span data-ttu-id="bcf7a-144">885</span><span class="sxs-lookup"><span data-stu-id="bcf7a-144">885</span></span> | <span data-ttu-id="bcf7a-145">2360</span><span class="sxs-lookup"><span data-stu-id="bcf7a-145">2360</span></span>   | <span data-ttu-id="bcf7a-146">167%</span><span class="sxs-lookup"><span data-stu-id="bcf7a-146">167%</span></span>

<span data-ttu-id="bcf7a-147">ブラウザーは HTTP 要求ヘッダーに関して非常に冗長です。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="bcf7a-148">合計バイト数では、バンドルときに、メトリックが大幅に削減を見たを送信します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="bcf7a-149">ただし、この例をローカルで実行、読み込み時間が大幅に向上を示します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="bcf7a-150">アセットをバンドルと縮小を使用して、ネットワーク経由で転送されたときに、大きなパフォーマンスの向上が実現されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="bcf7a-151">バンドルと縮小の戦略を選択します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="bcf7a-152">MVC と Razor ページ プロジェクト テンプレートは、バンドルと縮小の JSON 構成ファイルで構成されるため、ボックスのソリューションを提供します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="bcf7a-153">サード パーティ製ツールなど、 [Grunt](xref:client-side/using-grunt)ランナーをタスクで、もう少し複雑さで同じタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="bcf7a-154">開発ワークフローにはバンドルと縮小を超える処理が必要な場合、サード パーティのツールは非常によく適合&mdash;lint 処理と画像の最適化など。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="bcf7a-155">デザイン時のバンドルと縮小を使用すると、縮小されたファイルは、アプリのデプロイの前に作成されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="bcf7a-156">バンドルと縮小を展開する前に、サーバー負荷の軽減の利点を提供します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="bcf7a-157">ただし、そのデザイン時のバンドルを認識することが重要と縮小はビルド複雑さも増すし、静的ファイルでのみ動作します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="bcf7a-158">バンドルと縮小を構成します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="bcf7a-159">ASP.NET Core 2.0 以前では、MVC および Razor ページ プロジェクト テンプレートを提供する*bundleconfig.json*各バンドルするためのオプションを定義する構成ファイル。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="bcf7a-160">ASP.NET Core 2.1 以降では、追加、という名前の新しい JSON ファイル*bundleconfig.json*、Razor ページまたは MVC プロジェクトのルートにします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="bcf7a-161">開始点として、そのファイルに次の JSON を含めます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="bcf7a-162">*Bundleconfig.json*ファイルは、各バンドルするためのオプションを定義します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="bcf7a-163">カスタムの JavaScript の前の例では、1 つのバンドルの構成が定義されている (*wwwroot/js/site.js*) とスタイル シート (*wwwroot/css/site.css*) ファイル。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="bcf7a-164">構成オプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-164">Configuration options include:</span></span>

* <span data-ttu-id="bcf7a-165">`outputFileName`:出力するバンドル ファイルの名前。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="bcf7a-166">相対パスを含めることができます、 *bundleconfig.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="bcf7a-167">**必須**</span><span class="sxs-lookup"><span data-stu-id="bcf7a-167">**required**</span></span>
* <span data-ttu-id="bcf7a-168">`inputFiles`:一緒にバンドルするファイルの配列。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="bcf7a-169">これらは、構成ファイルへの相対パスです。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="bcf7a-170">**省略可能な**、\* が空の出力ファイルに空の値の結果します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="bcf7a-171">[グロビング](https://www.tldp.org/LDP/abs/html/globbingref.html)パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="bcf7a-172">`minify` :出力の種類を縮小するオプション。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="bcf7a-173">**省略可能な**、*既定 - `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="bcf7a-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="bcf7a-174">構成オプションは、出力ファイルの種類ごとに使用できます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="bcf7a-175">CSS の縮小化</span><span class="sxs-lookup"><span data-stu-id="bcf7a-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="bcf7a-176">JavaScript の縮小化</span><span class="sxs-lookup"><span data-stu-id="bcf7a-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="bcf7a-177">HTML の縮小化</span><span class="sxs-lookup"><span data-stu-id="bcf7a-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="bcf7a-178">`includeInProject`:プロジェクト ファイルに生成されたファイルを追加するかどうかを示すフラグします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="bcf7a-179">**省略可能な**、*既定値: false*</span><span class="sxs-lookup"><span data-stu-id="bcf7a-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="bcf7a-180">`sourceMap` :バンドルされているファイルのソース マップを生成するかどうかを示すフラグします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="bcf7a-181">**省略可能な**、*既定値: false*</span><span class="sxs-lookup"><span data-stu-id="bcf7a-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="bcf7a-182">`sourceMapRootPath`:生成されたソース マップ ファイルを保存するルート パス。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="bcf7a-183">バンドルと縮小のビルド時の実行</span><span class="sxs-lookup"><span data-stu-id="bcf7a-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="bcf7a-184">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet パッケージは、バンドルの実行とビルド時に縮小できるようにします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="bcf7a-185">パッケージを挿入します。 [MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)ビルドおよびクリーン時に実行します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="bcf7a-186">*Bundleconfig.json*定義済みの構成に基づいて、出力ファイルを生成するために、ビルド プロセスによってファイルを分析します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="bcf7a-187">BuildBundlerMinifier は、github の Microsoft サポートを提供コミュニティが主導のプロジェクトに属していません。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="bcf7a-188">問題を提出する必要があります[ここ](https://github.com/madskristensen/BundlerMinifier/issues)します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bcf7a-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bcf7a-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bcf7a-190">追加、 *BuildBundlerMinifier*をプロジェクトにパッケージします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="bcf7a-191">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-191">Build the project.</span></span> <span data-ttu-id="bcf7a-192">次の出力ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="bcf7a-193">プロジェクトをクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-193">Clean the project.</span></span> <span data-ttu-id="bcf7a-194">次の出力ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bcf7a-195">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bcf7a-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bcf7a-196">追加、 *BuildBundlerMinifier*パッケージをプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="bcf7a-197">ASP.NET を使用して場合 Core 1.x を新しく追加されたパッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```console
dotnet restore
```

<span data-ttu-id="bcf7a-198">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-198">Build the project:</span></span>

```console
dotnet build
```

<span data-ttu-id="bcf7a-199">次が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="bcf7a-200">プロジェクトをクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-200">Clean the project:</span></span>

```console
dotnet clean
```

<span data-ttu-id="bcf7a-201">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="bcf7a-202">バンドルと縮小のアドホック実行</span><span class="sxs-lookup"><span data-stu-id="bcf7a-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="bcf7a-203">プロジェクトをビルドせずにアドホック単位でバンドルや縮小タスクを実行することになります。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="bcf7a-204">追加、 [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/)をプロジェクトに NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="bcf7a-205">BundlerMinifier.Core は、github の Microsoft サポートを提供コミュニティが主導のプロジェクトに属していません。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="bcf7a-206">問題を提出する必要があります[ここ](https://github.com/madskristensen/BundlerMinifier/issues)します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="bcf7a-207">このパッケージに含める .NET Core CLI の拡張、 *dotnet バンドル*ツール。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="bcf7a-208">パッケージ マネージャー コンソール (PMC) ウィンドウ、またはコマンド シェルで、次のコマンドを実行できます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```console
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="bcf7a-209">NuGet パッケージ マネージャーとして、\*.csproj ファイルに依存関係を追加します`<PackageReference />`ノード。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="bcf7a-210">`dotnet bundle`コマンドが .NET Core CLI を使用して登録されている場合にのみ、`<DotNetCliToolReference />`ノードが使用されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="bcf7a-211">適宜、\*.csproj ファイルを変更します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="bcf7a-212">ファイルをワークフローに追加します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-212">Add files to workflow</span></span>

<span data-ttu-id="bcf7a-213">例を検討してください。 追加*custom.css* 、次のようなファイルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="bcf7a-214">縮小する*custom.css*でバンドルと*site.css*に、 *site.min.css*ファイルを追加する相対パス*bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="bcf7a-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="bcf7a-215">または、次のグロビング パターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> <span data-ttu-id="bcf7a-216">このグロビング パターンは、すべての CSS ファイルに一致し、縮小されたファイルのパターンを除外します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="bcf7a-217">アプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-217">Build the application.</span></span> <span data-ttu-id="bcf7a-218">開いている*site.min.css*の内容に注意してくださいと*custom.css*がファイルの末尾に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="bcf7a-219">環境ベースのバンドルと縮小</span><span class="sxs-lookup"><span data-stu-id="bcf7a-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="bcf7a-220">ベスト プラクティスとして、アプリのバンドルと縮小されたファイルは、運用環境で使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="bcf7a-221">開発中は、元のファイルは、アプリのデバッグを容易のこと。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="bcf7a-222">使用して、ページに含めるファイルを指定、[環境タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)ビューで。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="bcf7a-223">環境タグ ヘルパーは、特定の実行時にのみその内容をレンダリング[環境](xref:fundamentals/environments)します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bcf7a-224">次`environment`で実行する場合、タグが未処理の CSS ファイルをレンダリング、`Development`環境。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="bcf7a-225">次`environment`以外の環境で実行しているときに、タグがバンドルと縮小の CSS ファイルをレンダリング`Development`します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="bcf7a-226">たとえばで実行されている`Production`または`Staging`これらのスタイル シートのレンダリングをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="bcf7a-227">Gulp から bundleconfig.json を使用します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="bcf7a-228">アプリのバンドルと縮小のワークフローに追加の処理が必要とする場合があります。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="bcf7a-229">例には、イメージの最適化やキャッシュ バスティング、CDN 資産の処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="bcf7a-230">これらの要件を満たすためには、Gulp を使用するバンドルと縮小のワークフローに変換できます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="bcf7a-231">Bundler & Minifier 拡張機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="bcf7a-232">Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)拡張機能は、Gulp への変換を処理します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="bcf7a-233">Bundler & Minifier 拡張機能は、github の Microsoft サポートを提供しないコミュニティが主導のプロジェクトに属しています。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="bcf7a-234">問題を提出する必要があります[ここ](https://github.com/madskristensen/BundlerMinifier/issues)します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="bcf7a-235">右クリックし、 *bundleconfig.json*ソリューション エクスプ ローラーでファイルおよび選択**Bundler & Minifier** > **Gulp に変換しています.** :</span><span class="sxs-lookup"><span data-stu-id="bcf7a-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Gulp をで変換コンテキスト メニュー項目](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="bcf7a-237">*Gulpfile.js*と*package.json*ファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="bcf7a-238">サポートしている[npm](https://www.npmjs.com/)で示されているパッケージ、 *package.json*ファイルの`devDependencies`セクションがインストールされています。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="bcf7a-239">グローバルの依存関係として Gulp CLI をインストールする PMC ウィンドウで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="bcf7a-240">*Gulpfile.js*ファイルの読み取り、 *bundleconfig.json*ファイルの入力、出力、および設定します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="bcf7a-241">手動で変換します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-241">Convert manually</span></span>

<span data-ttu-id="bcf7a-242">Visual Studio や Bundler & Minifier 拡張機能を使用できない場合に、手動で変換します。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="bcf7a-243">追加、 *package.json*を次のファイル`devDependencies`プロジェクトのルート。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="bcf7a-244">`gulp-uglify` ECMAScript (ES) 2015 をサポートしていないモジュール/ES6 以降。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="bcf7a-245">インストール[gulp terser](https://www.npmjs.com/package/gulp-terser)の代わりに`gulp-uglify`ES2015 を使用する/ES6 以降。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="bcf7a-246">同じレベルで、次のコマンドを実行して、依存関係をインストール*package.json*:</span><span class="sxs-lookup"><span data-stu-id="bcf7a-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="bcf7a-247">グローバルの依存関係として Gulp CLI をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="bcf7a-248">コピー、 *gulpfile.js*をプロジェクトのルート以下のファイルします。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="bcf7a-249">Gulp タスクの実行</span><span class="sxs-lookup"><span data-stu-id="bcf7a-249">Run Gulp tasks</span></span>

<span data-ttu-id="bcf7a-250">Visual Studio でプロジェクトをビルドする前に、Gulp 縮小タスクをトリガーするには、次のコードを追加[MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)\*.csproj ファイルに。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="bcf7a-251">この例で、内で定義されたすべてのタスク、`MyPreCompileTarget`ターゲットを実行する前に、定義済み`Build`ターゲット。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="bcf7a-252">Visual Studio の出力ウィンドウで、次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bcf7a-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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


## <a name="additional-resources"></a><span data-ttu-id="bcf7a-253">その他の資料</span><span class="sxs-lookup"><span data-stu-id="bcf7a-253">Additional resources</span></span>

* [<span data-ttu-id="bcf7a-254">Grunt の使用</span><span class="sxs-lookup"><span data-stu-id="bcf7a-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="bcf7a-255">複数の環境の使用</span><span class="sxs-lookup"><span data-stu-id="bcf7a-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="bcf7a-256">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="bcf7a-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
