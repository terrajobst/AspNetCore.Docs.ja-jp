---
title: ASP.NET Core での静的資産のバンドルと縮小
author: scottaddie
description: バンドルと縮小の手法を適用して、ASP.NET Core Web アプリケーションの静的リソースを最適化する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646784"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core での静的資産のバンドルと縮小

作成者: [Scott Addie](https://twitter.com/Scott_Addie)、[David Pine](https://twitter.com/davidpine7)

この記事では、ASP.NET Core Web アプリでこれらの機能を使用する方法など、バンドルと縮小を適用する利点について説明します。

## <a name="what-is-bundling-and-minification"></a>バンドルと縮小とは

バンドルと縮小は、Web アプリに適用できる 2 種類のパフォーマンス最適化です。 バンドルと縮小を併用すると、サーバー要求の数が減り、要求される静的資産のサイズが小さくなるため、パフォーマンスが向上します。

バンドルと縮小を使用すると、主に最初のページ要求の読み込み時間が短縮されます。 Web ページが要求されると、ブラウザーによって静的資産 (JavaScript、CSS、および画像) がキャッシュされます。 そのため、同じ資産を要求する同じサイト上で、同じページまたは複数のページを要求する場合、バンドルと縮小を使用してもパフォーマンスは改善されません。 expires ヘッダーが資産に正しく設定されておらず、バンドルと縮小が使用されていない場合、ブラウザーの更新の間隔ヒューリスティックにより、数日後には資産が古くなっているとマークされます。 さらに、ブラウザーでは、各資産に対する検証要求が必要です。 この場合、最初のページ要求の後でも、バンドルと縮小によりパフォーマンスが向上します。

### <a name="bundling"></a>バンドル

バンドルでは、複数のファイルを単一のファイルに連結します。 バンドルを使用すると、Web ページなどの Web 資産のレンダリングに必要なサーバー要求の数を減らすことができます。 CSS、JavaScript など専用の個別のバンドルをいくつでも作成できます。ファイルが少なくなると、ブラウザーからサーバーへ、またはアプリケーションを提供するサービスからの HTTP 要求が少なくなることを意味します。 その結果、最初のページ読み込みのパフォーマンスが向上します。

### <a name="minification"></a>縮小

縮小を使用すると、機能を変更せずにコードから不要な文字を削除できます。 その結果、要求される資産 (CSS、画像、JavaScript ファイルなど) のサイズが大幅に減ります。 縮小の一般的な副作用として、変数名を 1 文字に短縮する機能や、コメントと不要な空白を削除する機能があります。

次の JavaScript 関数を考えてみます。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

縮小により、関数が次のように短縮されます。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

コメントと不要な空白の削除に加えて、次のパラメーターと変数名は次のように名前が変更されました。

元 | 名前の変更
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>バンドルと縮小の影響

次の表は、個別に資産を読み込む場合とバンドルと縮小を使用する場合の違いを示しています。

アクション | バンドルと縮小あり | バンドルと縮小なし | 変更
--- | :---: | :---: | :---:
ファイル要求  | 7   | 18     | 157%
転送済み (KB) | 156 | 264.68 | 70%
読み込み時間 (ミリ秒) | 885 | 2360   | 167%

ブラウザーは、HTTP 要求ヘッダーに関してかなり冗長です。 送信された合計バイト数メトリックは、バンドル時に大幅に減りました。 読み込み時間は大幅に改善されていますが、この例はローカルで実行されています。 ネットワークを経由で転送される資産にバンドルと縮小を使用すると、パフォーマンスが大幅に向上します。

## <a name="choose-a-bundling-and-minification-strategy"></a>バンドルと縮小の戦略を選択する

MVC および Razor Pages プロジェクト テンプレートには、JSON 構成ファイルで構成されるバンドルおよび縮小のすぐに使用できるソリューションが用意されています。 [Grunt](xref:client-side/using-grunt) タスク ランナーなどのサードパーティ ツールの場合、同じタスクを実行するにはもう少し複雑です。 サードパーティ製のツールは、リンティングや画像の最適化など、バンドルと縮小を超える処理が開発ワークフローに必要な場合に最適です。 設計時にバンドルと縮小を使用することで、アプリのデプロイ前に縮小されたファイルが作成されます。 デプロイ前のバンドルと縮小によって、サーバーの負荷が軽減されます。 ただし、設計時にバンドルと縮小を使用するとビルドの複雑さが増すので、静的ファイルでのみ機能することを認識することが重要です。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小を構成する

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core 2.0 以前では、MVC および Razor Pages プロジェクト テンプレートには、各バンドルのオプションが定義された *bundleconfig.json* 構成ファイルが用意されています。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.1 以降では、*bundleconfig.json* という名前の新しい JSON ファイルを MVC または Razor Pages プロジェクトのルートに追加します。 開始点としてそのファイルに次の JSON を含めます。

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*bundleconfig.json* ファイルには、各バンドルのオプションが定義されています。 上記の例では、カスタムの JavaScript (*wwwroot/js/site.js*) とスタイルシート (*wwwroot/css/site.css*) ファイルに対して 1 つのバンドル構成が定義されています。

構成のオプションには、次のようなものがあります。

* `outputFileName`:出力するバンドル ファイルの名前。 *bundleconfig.json* ファイルからの相対パスを含めることができます。 **必須**
* `inputFiles`:まとめてバンドルするファイルの配列。 これらは、構成ファイルへの相対パスです。 **省略可能**、* 値が空の場合、空の出力ファイルになります。 [globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) パラメーターがサポートされます。
* `minify`:出力の種類の縮小オプション。 **省略可能**、*既定値 - `minify: { enabled: true }`*
  * 構成オプションは、出力ファイルの種類ごとに使用できます。
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`:生成されたファイルをプロジェクト ファイルに追加するかどうかを示すフラグ。 **省略可能**、*既定値 - false*
* `sourceMap`:バンドルされたファイルのソース マップを生成するかどうかを示すフラグ。 **省略可能**、*既定値 - false*
* `sourceMapRootPath`:生成されたソース マップ ファイルを格納するためのルート パス。

## <a name="build-time-execution-of-bundling-and-minification"></a>バンドルと縮小のビルド時実行

[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet パッケージを使用すると、ビルド時にバンドルと縮小を実行できます。 このパッケージを使用すると、ビルドおよびクリーン時に実行される [MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)が挿入されます。 *bundleconfig.json* ファイルはビルド プロセスによって分析され、定義された構成に基づいて出力ファイルが生成されます。

> [!NOTE]
> BuildBundlerMinifier は、Microsoft がサポートを提供していない GitHub のコミュニティ主導のプロジェクトに属しています。 問題は[ここ](https://github.com/madskristensen/BundlerMinifier/issues)で提出する必要があります。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*BuildBundlerMinifier* パッケージをプロジェクトに追加します。

プロジェクトをビルドします。 出力ウィンドウに次のように表示されます。

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

プロジェクトを消去します。 出力ウィンドウに次のように表示されます。

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*BuildBundlerMinifier* パッケージをプロジェクトに追加します。

```dotnetcli
dotnet add package BuildBundlerMinifier
```

ASP.NET Core 1.x を使用している場合は、新しく追加したパッケージを復元します。

```dotnetcli
dotnet restore
```

プロジェクトをビルドします。

```dotnetcli
dotnet build
```

次のように表示されます。

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

プロジェクトを消去します。

```dotnetcli
dotnet clean
```

次の出力が表示されます。

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>バンドルと縮小のアドホック実行

プロジェクトをビルドせずに、アドホック ベースでバンドルおよび縮小タスクを実行できます。 [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet パッケージをプロジェクトに追加します。

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core は、Microsoft がサポートを提供していない GitHub のコミュニティ主導のプロジェクトに属しています。 問題は[ここ](https://github.com/madskristensen/BundlerMinifier/issues)で提出する必要があります。

このパッケージを使用すると、.NET Core CLI が拡張され、*dotnet-bundle* ツールが追加されます。 次のコマンドは、パッケージ マネージャー コンソール (PMC) ウィンドウまたはコマンド シェルで実行できます。

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> NuGet パッケージ マネージャーを使用すると、依存関係が `<PackageReference />` ノードとして *.csproj ファイルに追加されます。 `dotnet bundle` コマンドは、`<DotNetCliToolReference />` ノードが使用されている場合にのみ .NET Core CLI に登録されます。 必要に応じて *.csproj ファイルを変更します。

## <a name="add-files-to-workflow"></a>ワークフローにファイルを追加する

次のような新しい *custom.css* ファイルが追加される例を考えてみましょう。

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

*custom.css* を縮小し、それと *site.css* を *site.min.css* ファイルにバンドルするには、相対パスを *bundleconfig.json* に追加します。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> または、次の glob パターンを使用できます。
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> この glob パターンはすべての CSS ファイルを照合し、縮小されたファイル パターンは除外されます。

アプリケーションをビルドします。 *site.min.css* を開き、ファイルの最後に *custom.css* のコンテンツが追加されていることに注意します。

## <a name="environment-based-bundling-and-minification"></a>環境ベースのバンドルと縮小

ベスト プラクティスとして、アプリのバンドルおよび縮小されたファイルを運用環境で使用することをお勧めします。 開発中は、元のファイルがあるので、アプリのデバッグが容易になります。

ビューで [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) を使用して、ページに含めるファイルを指定します。 Environment Tag Helper を使用すると、特定の[環境](xref:fundamentals/environments)で実行されている場合にのみコンテンツがレンダリングされます。

`Development` 環境で実行されている場合、次の `environment` タグを使用すると、未処理の CSS ファイルがレンダリングされます。

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

`Development` 以外の環境で実行されている場合に、次の `environment` タグを使用すると、バンドルおよび縮小された CSS ファイルがレンダリングされます。 たとえば、`Production` または `Staging` で実行すると、これらのスタイルシートのレンダリングがトリガーされます。

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Gulp から bundleconfig.json を使用する

アプリのバンドルおよび縮小ワークフローで追加の処理が必要になる場合があります。 たとえば、画像の最適化、キャッシュ バスティング、CDN 資産の処理などです。 これらの要件を満たすために、Gulp を使用するようにバンドルおよび縮小ワークフローを変換できます。

### <a name="use-the-bundler--minifier-extension"></a>Bundler & Minifier 拡張機能を使用する

Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) 拡張機能を使用すると、Gulp への変換処理ができます。

> [!NOTE]
> Bundler & Minifier 拡張機能は、Microsoft がサポートを提供していない GitHub のコミュニティ主導のプロジェクトに属しています。 問題は[ここ](https://github.com/madskristensen/BundlerMinifier/issues)で提出する必要があります。

ソリューション エクスプローラーで *bundleconfig.json* ファイルを右クリックし、 **[Bundler & Minifier]**  >  **[Convert To Gulp]\(Gulp への変換\)** を選択します。

![[Convert To Gulp]\(Gulp への変換\) メニュー項目](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

*gulpfile.js* ファイルと *package.json* ファイルがプロジェクトに追加されます。 *package.json* ファイルの `devDependencies` セクションに記載されている、サポートされる [npm](https://www.npmjs.com/) パッケージがインストールされます。

PMC ウィンドウで次のコマンドを実行し、Gulp CLI をグローバルな依存関係としてインストールします。

```console
npm i -g gulp-cli
```

*gulpfile.js* ファイルからは、入力、出力、および設定について *bundleconfig.json* ファイルが読み取られます。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>手動で変換する

Visual Studio や Bundler & Minifier 拡張機能を使用できない場合は、手動で変換します。

次の `devDependencies` を使用して *package.json* ファイルをプロジェクトのルートに追加します。

> [!WARNING]
> `gulp-uglify` モジュールは、ECMAScript (ES) 2015 / ES6 以降をサポートしていません。 ES2015 / ES6 以降を使用するには、`gulp-uglify` ではなく [gulp-terser](https://www.npmjs.com/package/gulp-terser) をインストールします。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

*package.json* と同じレベルで次のコマンドを実行して、依存関係をインストールします。

```console
npm i
```

グローバルな依存関係として Gulp CLI をインストールします。

```console
npm i -g gulp-cli
```

次の *gulpfile.js* ファイルをプロジェクトのルートにコピーします。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp タスクを実行する

Visual Studio でプロジェクトをビルドする前に Gulp 縮小タスクをトリガーするには、次の [MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)を *.csproj ファイルに追加します。

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

この例では、`MyPreCompileTarget` ターゲット内で定義されたタスクは、事前に定義された `Build` ターゲットの前に実行されます。 次のような出力が Visual Studio の出力ウィンドウに表示されます。

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

## <a name="additional-resources"></a>その他の技術情報

* [Grunt の使用](xref:client-side/using-grunt)
* [複数の環境の使用](xref:fundamentals/environments)
* [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)
