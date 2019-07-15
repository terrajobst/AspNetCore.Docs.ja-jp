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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>バンドルし、縮小の ASP.NET Core で静的なアセット

によって[Scott Addie](https://twitter.com/Scott_Addie)と[David 松](https://twitter.com/davidpine7)

この記事では、バンドルや縮小、これらの機能を使用して ASP.NET Core web アプリの使用方法などを適用する利点について説明します。

## <a name="what-is-bundling-and-minification"></a>新機能のバンドルと縮小

バンドルと縮小は、web アプリに適用できる 2 つの個別のパフォーマンス最適化です。 を一緒に使用バンドルと縮小パフォーマンス向上のサーバー要求の数を減らすと、要求された静的なアセットのサイズを小さきます。

バンドルと縮小は、最初のページ要求の読み込み時間を向上させる主にします。 Web ページを要求されたら、ブラウザーは、静的なアセット (JavaScript、CSS、およびイメージ) をキャッシュします。 その結果、バンドルと縮小しないパフォーマンスを向上させる、同じページで、または同じ資産を要求するのと同じサイト上のページを要求するときにします。 場合、有効期限が切れる資産にヘッダーが正しく設定されていないし、バンドルと縮小が使用されていない場合、ブラウザーの鮮度ヒューリスティック マーク資産古い数日後。 さらに、ブラウザーでは、各資産の検証要求が必要です。 この場合は、バンドルと縮小は、最初のページ要求した後でもパフォーマンスの向上を提供します。

### <a name="bundling"></a>バンドル

1 つのファイルに複数のファイルを結合バンドルします。 バンドルは、web ページなどの web アセットを表示するために必要なサーバー要求の数を減らします。 CSS、JavaScript などの具体的には、任意の数の個別のバンドルを作成できます。少ないファイルでは、サーバーにブラウザーまたはアプリケーションを提供するサービスから HTTP の要求が減少を意味します。 この結果には、最初のページ読み込みのパフォーマンスが向上しました。

### <a name="minification"></a>縮小

縮小では、機能を変更することがなく、コードから不要な文字を削除します。 要求されたアセット (CSS、画像、JavaScript ファイルなど) に大きなサイズの削減になります。 縮小の一般的な副作用は、1 つの文字を変数名を短く、コメントや不要な空白文字を削除するなどがあります。

次の JavaScript 関数を検討してください。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

縮小では、次に関数を削減します。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

だけでなく、コメントと不要な空白文字を削除するには、次のパラメーターと変数名が名前を変更されました。

元 | 名前の変更
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>バンドルと縮小の影響

次の表では、個別に資産の読み込みとバンドルと縮小を使用して違いを示します。

アクション | B/m | B/分なし | [Change]
--- | :---: | :---: | :---:
ファイルの要求  | 7   | 18     | 157%
サポート技術情報の転送 | 156 | 264.68 | 70%
読み込み時間 (ミリ秒) | 885 | 2360   | 167%

ブラウザーは HTTP 要求ヘッダーに関して非常に冗長です。 合計バイト数では、バンドルときに、メトリックが大幅に削減を見たを送信します。 ただし、この例をローカルで実行、読み込み時間が大幅に向上を示します。 アセットをバンドルと縮小を使用して、ネットワーク経由で転送されたときに、大きなパフォーマンスの向上が実現されます。

## <a name="choose-a-bundling-and-minification-strategy"></a>バンドルと縮小の戦略を選択します。

MVC と Razor ページ プロジェクト テンプレートは、バンドルと縮小の JSON 構成ファイルで構成されるため、ボックスのソリューションを提供します。 サード パーティ製ツールなど、 [Grunt](xref:client-side/using-grunt)ランナーをタスクで、もう少し複雑さで同じタスクを実行します。 開発ワークフローにはバンドルと縮小を超える処理が必要な場合、サード パーティのツールは非常によく適合&mdash;lint 処理と画像の最適化など。 デザイン時のバンドルと縮小を使用すると、縮小されたファイルは、アプリのデプロイの前に作成されます。 バンドルと縮小を展開する前に、サーバー負荷の軽減の利点を提供します。 ただし、そのデザイン時のバンドルを認識することが重要と縮小はビルド複雑さも増すし、静的ファイルでのみ動作します。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小を構成します。

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core 2.0 以前では、MVC および Razor ページ プロジェクト テンプレートを提供する*bundleconfig.json*各バンドルするためのオプションを定義する構成ファイル。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.1 以降では、追加、という名前の新しい JSON ファイル*bundleconfig.json*、Razor ページまたは MVC プロジェクトのルートにします。 開始点として、そのファイルに次の JSON を含めます。

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*Bundleconfig.json*ファイルは、各バンドルするためのオプションを定義します。 カスタムの JavaScript の前の例では、1 つのバンドルの構成が定義されている (*wwwroot/js/site.js*) とスタイル シート (*wwwroot/css/site.css*) ファイル。

構成オプションは次のとおりです。

* `outputFileName`:出力するバンドル ファイルの名前。 相対パスを含めることができます、 *bundleconfig.json*ファイル。 **必須**
* `inputFiles`:一緒にバンドルするファイルの配列。 これらは、構成ファイルへの相対パスです。 **省略可能な**、* が空の出力ファイルに空の値の結果します。 [グロビング](https://www.tldp.org/LDP/abs/html/globbingref.html)パターンがサポートされています。
* `minify` :出力の種類を縮小するオプション。 **省略可能な**、*既定 - `minify: { enabled: true }`*
  * 構成オプションは、出力ファイルの種類ごとに使用できます。
    * [CSS の縮小化](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript の縮小化](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML の縮小化](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`:プロジェクト ファイルに生成されたファイルを追加するかどうかを示すフラグします。 **省略可能な**、*既定値: false*
* `sourceMap` :バンドルされているファイルのソース マップを生成するかどうかを示すフラグします。 **省略可能な**、*既定値: false*
* `sourceMapRootPath`:生成されたソース マップ ファイルを保存するルート パス。

## <a name="build-time-execution-of-bundling-and-minification"></a>バンドルと縮小のビルド時の実行

[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet パッケージは、バンドルの実行とビルド時に縮小できるようにします。 パッケージを挿入します。 [MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)ビルドおよびクリーン時に実行します。 *Bundleconfig.json*定義済みの構成に基づいて、出力ファイルを生成するために、ビルド プロセスによってファイルを分析します。

> [!NOTE]
> BuildBundlerMinifier は、github の Microsoft サポートを提供コミュニティが主導のプロジェクトに属していません。 問題を提出する必要があります[ここ](https://github.com/madskristensen/BundlerMinifier/issues)します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

追加、 *BuildBundlerMinifier*をプロジェクトにパッケージします。

プロジェクトをビルドします。 次の出力ウィンドウに表示されます。

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

プロジェクトをクリーンアップします。 次の出力ウィンドウに表示されます。

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

追加、 *BuildBundlerMinifier*パッケージをプロジェクト。

```console
dotnet add package BuildBundlerMinifier
```

ASP.NET を使用して場合 Core 1.x を新しく追加されたパッケージを復元します。

```console
dotnet restore
```

プロジェクトをビルドします。

```console
dotnet build
```

次が表示されます。

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

プロジェクトをクリーンアップします。

```console
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

プロジェクトをビルドせずにアドホック単位でバンドルや縮小タスクを実行することになります。 追加、 [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/)をプロジェクトに NuGet パッケージ。

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core は、github の Microsoft サポートを提供コミュニティが主導のプロジェクトに属していません。 問題を提出する必要があります[ここ](https://github.com/madskristensen/BundlerMinifier/issues)します。

このパッケージに含める .NET Core CLI の拡張、 *dotnet バンドル*ツール。 パッケージ マネージャー コンソール (PMC) ウィンドウ、またはコマンド シェルで、次のコマンドを実行できます。

```console
dotnet bundle
```

> [!IMPORTANT]
> NuGet パッケージ マネージャーとして、*.csproj ファイルに依存関係を追加します`<PackageReference />`ノード。 `dotnet bundle`コマンドが .NET Core CLI を使用して登録されている場合にのみ、`<DotNetCliToolReference />`ノードが使用されます。 適宜、*.csproj ファイルを変更します。

## <a name="add-files-to-workflow"></a>ファイルをワークフローに追加します。

例を検討してください。 追加*custom.css* 、次のようなファイルが追加されます。

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

縮小する*custom.css*でバンドルと*site.css*に、 *site.min.css*ファイルを追加する相対パス*bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> または、次のグロビング パターンを使用できます。
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> このグロビング パターンは、すべての CSS ファイルに一致し、縮小されたファイルのパターンを除外します。

アプリケーションをビルドします。 開いている*site.min.css*の内容に注意してくださいと*custom.css*がファイルの末尾に追加されます。

## <a name="environment-based-bundling-and-minification"></a>環境ベースのバンドルと縮小

ベスト プラクティスとして、アプリのバンドルと縮小されたファイルは、運用環境で使用する必要があります。 開発中は、元のファイルは、アプリのデバッグを容易のこと。

使用して、ページに含めるファイルを指定、[環境タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)ビューで。 環境タグ ヘルパーは、特定の実行時にのみその内容をレンダリング[環境](xref:fundamentals/environments)します。

次`environment`で実行する場合、タグが未処理の CSS ファイルをレンダリング、`Development`環境。

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

次`environment`以外の環境で実行しているときに、タグがバンドルと縮小の CSS ファイルをレンダリング`Development`します。 たとえばで実行されている`Production`または`Staging`これらのスタイル シートのレンダリングをトリガーします。

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Gulp から bundleconfig.json を使用します。

アプリのバンドルと縮小のワークフローに追加の処理が必要とする場合があります。 例には、イメージの最適化やキャッシュ バスティング、CDN 資産の処理が含まれます。 これらの要件を満たすためには、Gulp を使用するバンドルと縮小のワークフローに変換できます。

### <a name="use-the-bundler--minifier-extension"></a>Bundler & Minifier 拡張機能を使用します。

Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)拡張機能は、Gulp への変換を処理します。

> [!NOTE]
> Bundler & Minifier 拡張機能は、github の Microsoft サポートを提供しないコミュニティが主導のプロジェクトに属しています。 問題を提出する必要があります[ここ](https://github.com/madskristensen/BundlerMinifier/issues)します。

右クリックし、 *bundleconfig.json*ソリューション エクスプ ローラーでファイルおよび選択**Bundler & Minifier** > **Gulp に変換しています.** :

![Gulp をで変換コンテキスト メニュー項目](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

*Gulpfile.js*と*package.json*ファイルがプロジェクトに追加されます。 サポートしている[npm](https://www.npmjs.com/)で示されているパッケージ、 *package.json*ファイルの`devDependencies`セクションがインストールされています。

グローバルの依存関係として Gulp CLI をインストールする PMC ウィンドウで、次のコマンドを実行します。

```console
npm i -g gulp-cli
```

*Gulpfile.js*ファイルの読み取り、 *bundleconfig.json*ファイルの入力、出力、および設定します。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>手動で変換します。

Visual Studio や Bundler & Minifier 拡張機能を使用できない場合に、手動で変換します。

追加、 *package.json*を次のファイル`devDependencies`プロジェクトのルート。

> [!WARNING]
> `gulp-uglify` ECMAScript (ES) 2015 をサポートしていないモジュール/ES6 以降。 インストール[gulp terser](https://www.npmjs.com/package/gulp-terser)の代わりに`gulp-uglify`ES2015 を使用する/ES6 以降。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

同じレベルで、次のコマンドを実行して、依存関係をインストール*package.json*:

```console
npm i
```

グローバルの依存関係として Gulp CLI をインストールします。

```console
npm i -g gulp-cli
```

コピー、 *gulpfile.js*をプロジェクトのルート以下のファイルします。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp タスクの実行

Visual Studio でプロジェクトをビルドする前に、Gulp 縮小タスクをトリガーするには、次のコードを追加[MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)*.csproj ファイルに。

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

この例で、内で定義されたすべてのタスク、`MyPreCompileTarget`ターゲットを実行する前に、定義済み`Build`ターゲット。 Visual Studio の出力ウィンドウで、次のような出力が表示されます。

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


## <a name="additional-resources"></a>その他の資料

* [Grunt の使用](xref:client-side/using-grunt)
* [複数の環境の使用](xref:fundamentals/environments)
* [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)
