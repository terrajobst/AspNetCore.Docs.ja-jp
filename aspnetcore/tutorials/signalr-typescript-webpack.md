---
title: TypeScript と Webpack で ASP.NET Core SignalR を使用する
author: ssougnez
description: このチュートリアルでは、クライアントが TypeScript で記述された ASP.NET Core SignalR Web アプリをバンドルおよびビルドするために Webpack を構成します。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: a7c99c9e79647995886aec5b3a91584fd2f24451
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317488"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="2e727-103">TypeScript と Webpack で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="2e727-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="2e727-104">作成者: [Sébastien Sougnez](https://twitter.com/ssougnez)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2e727-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2e727-105">[Webpack](https://webpack.js.org/) を使用すると、開発者は Web アプリのクライアント側のリソースをバンドルおよびビルドすることができます。</span><span class="sxs-lookup"><span data-stu-id="2e727-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="2e727-106">このチュートリアルでは、クライアントが [TypeScript](https://www.typescriptlang.org/) で記述された ASP.NET Core SignalR Web アプリでの Webpack の使用法を示します。</span><span class="sxs-lookup"><span data-stu-id="2e727-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="2e727-107">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2e727-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e727-108">スターター ASP.NET Core SignalR アプリをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="2e727-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="2e727-109">SignalR TypeScript クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="2e727-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="2e727-110">Webpack を使用してビルド パイプラインを構成する</span><span class="sxs-lookup"><span data-stu-id="2e727-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="2e727-111">SignalR サーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="2e727-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="2e727-112">クライアントとサーバー間の通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="2e727-112">Enable communication between client and server</span></span>

<span data-ttu-id="2e727-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2e727-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="2e727-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2e727-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e727-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e727-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e727-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="2e727-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="2e727-117">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="2e727-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="2e727-118">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="2e727-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e727-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="2e727-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="2e727-121">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="2e727-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="2e727-122">C# for Visual Studio Code バージョン 1.17.1 またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="2e727-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="2e727-123">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="2e727-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="2e727-124">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2e727-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e727-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e727-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e727-126">*PATH* 環境変数で npm を検索するように Visual Studio を構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="2e727-127">既定では、Visual Studio は、そのインストール ディレクトリ内で見つかった npm のバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2e727-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="2e727-128">Visual Studio のこれらの説明に従ってください。</span><span class="sxs-lookup"><span data-stu-id="2e727-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="2e727-129">**[ツール]** > **[オプション]** > **[プロジェクトとソリューション]** > **[Web パッケージ管理]** > **[外部 Web ツール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e727-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="2e727-130">リストから *[$(PATH)]* エントリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="2e727-131">上向き矢印をクリックして、エントリをリストの 2 番目の位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e727-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio の構成](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="2e727-133">Visual Studio の構成が完了しました。</span><span class="sxs-lookup"><span data-stu-id="2e727-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="2e727-134">次はプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-134">It's time to create the project.</span></span>

1. <span data-ttu-id="2e727-135">**[ファイル]** > **[新規]** > **[プロジェクト]** メニュー オプションを使用して、 **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="2e727-136">プロジェクトに *SignalRWebPack* という名前を付け、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="2e727-137">ターゲット フレームワークのドロップダウンから *[.NET Core]* を選択し、フレームワーク セレクターのドロップダウンから *[ASP.NET Core 3.0]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="2e727-138">**空**のテンプレートを選択して、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e727-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e727-140">**統合端末**で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="2e727-141">.NET Core をターゲットとする、空の ASP.NET Core Web アプリが *SignalRWebPack* ディレクトリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="2e727-142">WebPack および TypeScript の構成</span><span class="sxs-lookup"><span data-stu-id="2e727-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="2e727-143">次の手順では、TypeScript の JavaScript への変換と、クライアント側のリソースのバンドルを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="2e727-144">プロジェクト ルートで次のコマンドを実行して、*package.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="2e727-145">強調表示されているプロパティを *package.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="2e727-146">`private` プロパティを `true` に設定して、次の手順でパッケージのインストールの警告が表示されないようにします。</span><span class="sxs-lookup"><span data-stu-id="2e727-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="2e727-147">必要な npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2e727-147">Install the required npm packages.</span></span> <span data-ttu-id="2e727-148">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="2e727-149">注目するべきコマンドの詳細:</span><span class="sxs-lookup"><span data-stu-id="2e727-149">Some command details to note:</span></span>

    * <span data-ttu-id="2e727-150">バージョン番号は、各パッケージ名の `@` 記号の後に続きます。</span><span class="sxs-lookup"><span data-stu-id="2e727-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="2e727-151">npm によって、これらの特定のパッケージ バージョンがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="2e727-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="2e727-152">`-E` オプションは、[セマンティック バージョニング](https://semver.org/)範囲演算子を *package.json* に書き込む npm の既定の動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2e727-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="2e727-153">たとえば、`"webpack": "4.29.3"` が `"webpack": "^4.29.3"` の代わりに使用されています。</span><span class="sxs-lookup"><span data-stu-id="2e727-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="2e727-154">このオプションにより、新しいパッケージ バージョンへの予期しないアップグレードが防止されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="2e727-155">詳細については、[npm-install](https://docs.npmjs.com/cli/install) の公式ドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e727-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="2e727-156">*package.json* ファイルの `scripts` プロパティを次のスニペットで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e727-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="2e727-157">スクリプトの説明 (一部):</span><span class="sxs-lookup"><span data-stu-id="2e727-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="2e727-158">`build`: 開発モードでクライアント側のリソースをバンドルし、ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="2e727-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="2e727-159">ファイル監視により、プロジェクト ファイルを変更するたびにバンドルが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="2e727-160">`mode` オプションは、ツリー シェイキングや縮小などの運用環境の最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2e727-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="2e727-161">`build` は開発でのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="2e727-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="2e727-162">`release`: 運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="2e727-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="2e727-163">`publish`: `release` スクリプトを実行して、運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="2e727-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="2e727-164">.NET Core CLI の [publish](/dotnet/core/tools/dotnet-publish) コマンドを呼び出してアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="2e727-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="2e727-165">プロジェクト ルートに、次の内容を持つ *webpack.config.js* という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="2e727-166">上記のファイルは、Webpack コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="2e727-167">注目するべき構成の詳細 (一部):</span><span class="sxs-lookup"><span data-stu-id="2e727-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="2e727-168">`output` プロパティにより、*dist* の既定値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="2e727-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="2e727-169">代わりにバンドルが *wwwroot* ディレクトリ内に生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="2e727-170">`resolve.extensions` 配列には、SignalR クライアント JavaScript をインポートするための *.js* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e727-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="2e727-171">プロジェクト ルートに新しい *src* プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="2e727-172">その目的は、プロジェクトのクライアント側アセットを格納することです。</span><span class="sxs-lookup"><span data-stu-id="2e727-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="2e727-173">次のコンテンツを含む *src/index.html* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="2e727-174">上記の HTML では、ホームページの定型マークアップが定義されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="2e727-175">新しい *src/css* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="2e727-176">その目的は、プロジェクトの *.css* ファイルを格納することです。</span><span class="sxs-lookup"><span data-stu-id="2e727-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="2e727-177">次のコンテンツを含む *src/css/main.css* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="2e727-178">上記の *main.css* ファイルは、アプリをスタイル設定します。</span><span class="sxs-lookup"><span data-stu-id="2e727-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="2e727-179">次のコンテンツを含む *src/tsconfig.json* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="2e727-180">上記のコードは、[ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 対応の JavaScript を生成するように TypeScript コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="2e727-181">次のコンテンツを含む *src/index.ts* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="2e727-182">上記の TypeScript は、DOM 要素への参照を取得し、次の 2 つのイベント ハンドラーをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="2e727-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="2e727-183">`keyup`: ユーザーがテキスト ボックスに `tbMessage` として識別されるものを入力すると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="2e727-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="2e727-184">ユーザーが **Enter** キーを押すと、`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="2e727-185">`click`: ユーザーが **[送信]** ボタンをクリックすると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="2e727-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="2e727-186">`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="2e727-187">ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="2e727-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="2e727-188">`Startup.Configure` メソッドで、[UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) および [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="2e727-189">上記のコードにより、サーバーが *index.html* ファイルを見つけて提供することができます。ユーザーがファイルの完全な URL または Web アプリのルート URL を入力するかどうかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="2e727-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="2e727-190">`Startup.Configure` メソッドの最後で、 */hub* ルートを `ChatHub` ハブにマップします。</span><span class="sxs-lookup"><span data-stu-id="2e727-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="2e727-191">*Hello World!* を表示するコードを</span><span class="sxs-lookup"><span data-stu-id="2e727-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="2e727-192">次の行に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e727-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="2e727-193">`Startup.ConfigureServices` メソッドで、[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2e727-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="2e727-194">これにより SignalR サービスがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="2e727-195">プロジェクト ルートに *Hubs* という新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="2e727-196">その目的は、次の手順で作成される SignalR ハブを格納することです。</span><span class="sxs-lookup"><span data-stu-id="2e727-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="2e727-197">次のコードを使用して、*Hubs/ChatHub.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="2e727-198">`ChatHub` 参照を解決するには、次のコードを *Startup.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="2e727-199">クライアントとサーバーの通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="2e727-199">Enable client and server communication</span></span>

<span data-ttu-id="2e727-200">アプリには現在、メッセージを送信するための単純なフォームが表示されています。</span><span class="sxs-lookup"><span data-stu-id="2e727-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="2e727-201">送信しようとしても、何も起こりません。</span><span class="sxs-lookup"><span data-stu-id="2e727-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="2e727-202">サーバーは特定のルートをリッスンしていますが、メッセージの送信については何も行いません。</span><span class="sxs-lookup"><span data-stu-id="2e727-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="2e727-203">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="2e727-204">上記のコマンドにより [SignalR TypeScript クライアント](https://www.npmjs.com/package/@microsoft/signalr) がインストールされ、クライアントがサーバーにメッセージを送信できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2e727-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="2e727-205">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="2e727-206">上記のコードは、サーバーからのメッセージの受信をサポートします。</span><span class="sxs-lookup"><span data-stu-id="2e727-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="2e727-207">`HubConnectionBuilder` クラスは、サーバー接続を構成するための新しいビルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="2e727-208">`withUrl` 関数は、ハブ URL を構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-208">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="2e727-209"> により、クライアントとサーバー間でのメッセージのやり取りが可能になります。</span><span class="sxs-lookup"><span data-stu-id="2e727-209"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="2e727-210">各メッセージには特定の名前があります。</span><span class="sxs-lookup"><span data-stu-id="2e727-210">Each message has a specific name.</span></span> <span data-ttu-id="2e727-211">たとえば、メッセージ ゾーンに新しいメッセージを表示するためのロジックを実行する `messageReceived` という名前を持つメッセージを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="2e727-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="2e727-212">特定のメッセージをリッスンするには、`on` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e727-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="2e727-213">任意の数のメッセージ名をリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="2e727-213">You can listen to any number of message names.</span></span> <span data-ttu-id="2e727-214">作成者の名前や受信したメッセージの内容など、パラメーターをメッセージに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="2e727-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="2e727-215">クライアントがメッセージを受信すると、`innerHTML` 属性に作成者の名前とメッセージ コンテンツを持つ新しい `div` 要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="2e727-216">これはメッセージを表示する主要な `div` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="2e727-217">これでクライアントがメッセージを受信できるようになったので、メッセージを送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="2e727-218">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="2e727-219">WebSocket 接続を介してメッセージを送信するには、`send` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e727-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="2e727-220">メソッドの最初のパラメーターは、メッセージ名です。</span><span class="sxs-lookup"><span data-stu-id="2e727-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="2e727-221">メッセージ データは、他のパラメーターに存在しています。</span><span class="sxs-lookup"><span data-stu-id="2e727-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="2e727-222">この例では、`newMessage` として識別されたメッセージがサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="2e727-223">メッセージは、ユーザー名と、テキスト ボックスへのユーザー入力で構成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="2e727-224">送信が機能していると、テキスト ボックスの値はクリアされます。</span><span class="sxs-lookup"><span data-stu-id="2e727-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="2e727-225">強調表示されているメソッドを `ChatHub` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="2e727-226">上記のコードは、サーバーがメッセージを受信すると、受信したメッセージをすべての接続されているユーザーにブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="2e727-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="2e727-227">すべてのメッセージを受信するのに、ジェネリック `on` メソッドは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2e727-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="2e727-228">メソッドはメッセージ名のサフィックスに基づいて名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2e727-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="2e727-229">この例では、TypeScript クライアントが `newMessage` として識別されるメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="2e727-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="2e727-230">C# `NewMessage` メソッドは、データがクライアントから送信されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="2e727-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="2e727-231">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) で [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="2e727-232">受信したメッセージは、ハブに接続されているすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="2e727-233">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2e727-233">Test the app</span></span>

<span data-ttu-id="2e727-234">次の手順で、アプリの動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="2e727-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e727-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e727-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2e727-236">*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="2e727-237">**パッケージ マネージャー コンソール** ウィンドウを使用して、プロジェクト ルートで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="2e727-238">プロジェクト ルートにいない場合は、コマンドを入力する前に `cd SignalRWebPack` と入力します。</span><span class="sxs-lookup"><span data-stu-id="2e727-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2e727-239">**[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、デバッガーをアタッチせずに、ブラウザーでアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2e727-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="2e727-240">*wwroot/index.html* ファイルは `http://localhost:<port_number>` で提供されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="2e727-241">コンパイル エラーが発生した場合は、ソリューションを閉じてから再度開いてみてください。</span><span class="sxs-lookup"><span data-stu-id="2e727-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="2e727-242">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="2e727-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="2e727-243">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="2e727-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2e727-244">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e727-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2e727-245">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e727-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2e727-247">プロジェクト ルートで次のコマンドを実行して、*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2e727-248">プロジェクト ルートで次のコマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="2e727-249">Web サーバーは、アプリを起動して、localhost 上で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="2e727-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="2e727-250">ブラウザーを開いて `http://localhost:<port_number>` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2e727-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="2e727-251">*wwwroot/index.html* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="2e727-252">アドレス バーから URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2e727-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="2e727-253">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="2e727-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="2e727-254">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="2e727-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2e727-255">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e727-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2e727-256">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![両方のブラウザー ウィンドウに表示されるメッセージ](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e727-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e727-258">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e727-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="2e727-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="2e727-260">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="2e727-260">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="2e727-261">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="2e727-261">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e727-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="2e727-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-263">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="2e727-264">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="2e727-264">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="2e727-265">C# for Visual Studio Code バージョン 1.17.1 またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="2e727-265">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="2e727-266">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="2e727-266">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="2e727-267">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2e727-267">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e727-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e727-268">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e727-269">*PATH* 環境変数で npm を検索するように Visual Studio を構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-269">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="2e727-270">既定では、Visual Studio は、そのインストール ディレクトリ内で見つかった npm のバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2e727-270">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="2e727-271">Visual Studio のこれらの説明に従ってください。</span><span class="sxs-lookup"><span data-stu-id="2e727-271">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="2e727-272">**[ツール]** > **[オプション]** > **[プロジェクトとソリューション]** > **[Web パッケージ管理]** > **[外部 Web ツール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e727-272">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="2e727-273">リストから *[$(PATH)]* エントリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-273">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="2e727-274">上向き矢印をクリックして、エントリをリストの 2 番目の位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e727-274">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio の構成](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="2e727-276">Visual Studio の構成が完了しました。</span><span class="sxs-lookup"><span data-stu-id="2e727-276">Visual Studio configuration is completed.</span></span> <span data-ttu-id="2e727-277">次はプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-277">It's time to create the project.</span></span>

1. <span data-ttu-id="2e727-278">**[ファイル]** > **[新規]** > **[プロジェクト]** メニュー オプションを使用して、 **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-278">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="2e727-279">プロジェクトに *SignalRWebPack* という名前を付け、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-279">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="2e727-280">ターゲット フレームワークのドロップダウンから、 *[.NET Core]* を選択し、フレームワーク セレクターのドロップダウンから *[ASP.NET Core 2.2]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-280">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="2e727-281">**空**のテンプレートを選択して、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e727-281">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e727-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e727-283">**統合端末**で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-283">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="2e727-284">.NET Core をターゲットとする、空の ASP.NET Core Web アプリが *SignalRWebPack* ディレクトリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-284">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="2e727-285">WebPack および TypeScript の構成</span><span class="sxs-lookup"><span data-stu-id="2e727-285">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="2e727-286">次の手順では、TypeScript の JavaScript への変換と、クライアント側のリソースのバンドルを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-286">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="2e727-287">プロジェクト ルートで次のコマンドを実行して、*package.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-287">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="2e727-288">強調表示されているプロパティを *package.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-288">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="2e727-289">`private` プロパティを `true` に設定して、次の手順でパッケージのインストールの警告が表示されないようにします。</span><span class="sxs-lookup"><span data-stu-id="2e727-289">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="2e727-290">必要な npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2e727-290">Install the required npm packages.</span></span> <span data-ttu-id="2e727-291">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-291">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="2e727-292">注目するべきコマンドの詳細:</span><span class="sxs-lookup"><span data-stu-id="2e727-292">Some command details to note:</span></span>

    * <span data-ttu-id="2e727-293">バージョン番号は、各パッケージ名の `@` 記号の後に続きます。</span><span class="sxs-lookup"><span data-stu-id="2e727-293">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="2e727-294">npm によって、これらの特定のパッケージ バージョンがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="2e727-294">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="2e727-295">`-E` オプションは、[セマンティック バージョニング](https://semver.org/)範囲演算子を *package.json* に書き込む npm の既定の動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2e727-295">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="2e727-296">たとえば、`"webpack": "4.29.3"` が `"webpack": "^4.29.3"` の代わりに使用されています。</span><span class="sxs-lookup"><span data-stu-id="2e727-296">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="2e727-297">このオプションにより、新しいパッケージ バージョンへの予期しないアップグレードが防止されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-297">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="2e727-298">詳細については、[npm-install](https://docs.npmjs.com/cli/install) の公式ドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e727-298">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="2e727-299">*package.json* ファイルの `scripts` プロパティを次のスニペットで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e727-299">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="2e727-300">スクリプトの説明 (一部):</span><span class="sxs-lookup"><span data-stu-id="2e727-300">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="2e727-301">`build`: 開発モードでクライアント側のリソースをバンドルし、ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="2e727-301">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="2e727-302">ファイル監視により、プロジェクト ファイルを変更するたびにバンドルが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-302">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="2e727-303">`mode` オプションは、ツリー シェイキングや縮小などの運用環境の最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2e727-303">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="2e727-304">`build` は開発でのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="2e727-304">Only use `build` in development.</span></span>
    * <span data-ttu-id="2e727-305">`release`: 運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="2e727-305">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="2e727-306">`publish`: `release` スクリプトを実行して、運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="2e727-306">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="2e727-307">.NET Core CLI の [publish](/dotnet/core/tools/dotnet-publish) コマンドを呼び出してアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="2e727-307">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="2e727-308">プロジェクト ルートに、次の内容を持つ *webpack.config.js* という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-308">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="2e727-309">上記のファイルは、Webpack コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-309">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="2e727-310">注目するべき構成の詳細 (一部):</span><span class="sxs-lookup"><span data-stu-id="2e727-310">Some configuration details to note:</span></span>

    * <span data-ttu-id="2e727-311">`output` プロパティにより、*dist* の既定値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="2e727-311">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="2e727-312">代わりにバンドルが *wwwroot* ディレクトリ内に生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-312">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="2e727-313">`resolve.extensions` 配列には、SignalR クライアント JavaScript をインポートするための *.js* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e727-313">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="2e727-314">プロジェクト ルートに新しい *src* プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-314">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="2e727-315">その目的は、プロジェクトのクライアント側アセットを格納することです。</span><span class="sxs-lookup"><span data-stu-id="2e727-315">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="2e727-316">次のコンテンツを含む *src/index.html* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-316">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="2e727-317">上記の HTML では、ホームページの定型マークアップが定義されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-317">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="2e727-318">新しい *src/css* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-318">Create a new *src/css* directory.</span></span> <span data-ttu-id="2e727-319">その目的は、プロジェクトの *.css* ファイルを格納することです。</span><span class="sxs-lookup"><span data-stu-id="2e727-319">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="2e727-320">次のコンテンツを含む *src/css/main.css* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-320">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="2e727-321">上記の *main.css* ファイルは、アプリをスタイル設定します。</span><span class="sxs-lookup"><span data-stu-id="2e727-321">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="2e727-322">次のコンテンツを含む *src/tsconfig.json* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-322">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="2e727-323">上記のコードは、[ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 対応の JavaScript を生成するように TypeScript コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-323">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="2e727-324">次のコンテンツを含む *src/index.ts* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-324">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="2e727-325">上記の TypeScript は、DOM 要素への参照を取得し、次の 2 つのイベント ハンドラーをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="2e727-325">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="2e727-326">`keyup`: ユーザーがテキスト ボックスに `tbMessage` として識別されるものを入力すると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="2e727-326">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="2e727-327">ユーザーが **Enter** キーを押すと、`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-327">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="2e727-328">`click`: ユーザーが **[送信]** ボタンをクリックすると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="2e727-328">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="2e727-329">`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-329">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="2e727-330">ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="2e727-330">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="2e727-331">`Startup.Configure` メソッドで提供されたコードにより、*Hello World!* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-331">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="2e727-332">`app.Run` メソッド呼び出しを、[UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) と [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) の呼び出しで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e727-332">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="2e727-333">上記のコードにより、サーバーが *index.html* ファイルを見つけて提供することができます。ユーザーがファイルの完全な URL または Web アプリのルート URL を入力するかどうかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="2e727-333">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="2e727-334">`Startup.ConfigureServices` メソッドで [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2e727-334">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2e727-335">これにより SignalR サービスがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-335">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="2e727-336">*/hub* ルートを `ChatHub` ハブにマップします。</span><span class="sxs-lookup"><span data-stu-id="2e727-336">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="2e727-337">`Startup.Configure` メソッドの末尾に、次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-337">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="2e727-338">プロジェクト ルートに *Hubs* という新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-338">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="2e727-339">その目的は、次の手順で作成される SignalR ハブを格納することです。</span><span class="sxs-lookup"><span data-stu-id="2e727-339">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="2e727-340">次のコードを使用して、*Hubs/ChatHub.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-340">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="2e727-341">`ChatHub` 参照を解決するには、次のコードを *Startup.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-341">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="2e727-342">クライアントとサーバーの通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="2e727-342">Enable client and server communication</span></span>

<span data-ttu-id="2e727-343">アプリには現在、メッセージを送信するための単純なフォームが表示されています。</span><span class="sxs-lookup"><span data-stu-id="2e727-343">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="2e727-344">送信しようとしても、何も起こりません。</span><span class="sxs-lookup"><span data-stu-id="2e727-344">Nothing happens when you try to do so.</span></span> <span data-ttu-id="2e727-345">サーバーは特定のルートをリッスンしていますが、メッセージの送信については何も行いません。</span><span class="sxs-lookup"><span data-stu-id="2e727-345">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="2e727-346">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-346">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="2e727-347">上記のコマンドにより [SignalR TypeScript クライアント](https://www.npmjs.com/package/@microsoft/signalr) がインストールされ、クライアントがサーバーにメッセージを送信できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2e727-347">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="2e727-348">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-348">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="2e727-349">上記のコードは、サーバーからのメッセージの受信をサポートします。</span><span class="sxs-lookup"><span data-stu-id="2e727-349">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="2e727-350">`HubConnectionBuilder` クラスは、サーバー接続を構成するための新しいビルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-350">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="2e727-351">`withUrl` 関数は、ハブ URL を構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-351">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="2e727-352"> により、クライアントとサーバー間でのメッセージのやり取りが可能になります。</span><span class="sxs-lookup"><span data-stu-id="2e727-352"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="2e727-353">各メッセージには特定の名前があります。</span><span class="sxs-lookup"><span data-stu-id="2e727-353">Each message has a specific name.</span></span> <span data-ttu-id="2e727-354">たとえば、メッセージ ゾーンに新しいメッセージを表示するためのロジックを実行する `messageReceived` という名前を持つメッセージを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="2e727-354">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="2e727-355">特定のメッセージをリッスンするには、`on` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e727-355">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="2e727-356">任意の数のメッセージ名をリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="2e727-356">You can listen to any number of message names.</span></span> <span data-ttu-id="2e727-357">作成者の名前や受信したメッセージの内容など、パラメーターをメッセージに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="2e727-357">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="2e727-358">クライアントがメッセージを受信すると、`innerHTML` 属性に作成者の名前とメッセージ コンテンツを持つ新しい `div` 要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-358">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="2e727-359">これはメッセージを表示する主要な `div` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-359">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="2e727-360">これでクライアントがメッセージを受信できるようになったので、メッセージを送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="2e727-360">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="2e727-361">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-361">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="2e727-362">WebSocket 接続を介してメッセージを送信するには、`send` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e727-362">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="2e727-363">メソッドの最初のパラメーターは、メッセージ名です。</span><span class="sxs-lookup"><span data-stu-id="2e727-363">The method's first parameter is the message name.</span></span> <span data-ttu-id="2e727-364">メッセージ データは、他のパラメーターに存在しています。</span><span class="sxs-lookup"><span data-stu-id="2e727-364">The message data inhabits the other parameters.</span></span> <span data-ttu-id="2e727-365">この例では、`newMessage` として識別されたメッセージがサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-365">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="2e727-366">メッセージは、ユーザー名と、テキスト ボックスへのユーザー入力で構成されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-366">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="2e727-367">送信が機能していると、テキスト ボックスの値はクリアされます。</span><span class="sxs-lookup"><span data-stu-id="2e727-367">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="2e727-368">強調表示されているメソッドを `ChatHub` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e727-368">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="2e727-369">上記のコードは、サーバーがメッセージを受信すると、受信したメッセージをすべての接続されているユーザーにブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="2e727-369">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="2e727-370">すべてのメッセージを受信するのに、ジェネリック `on` メソッドは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2e727-370">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="2e727-371">メソッドはメッセージ名のサフィックスに基づいて名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2e727-371">A method named after the message name suffices.</span></span>

    <span data-ttu-id="2e727-372">この例では、TypeScript クライアントが `newMessage` として識別されるメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="2e727-372">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="2e727-373">C# `NewMessage` メソッドは、データがクライアントから送信されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="2e727-373">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="2e727-374">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) で [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-374">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="2e727-375">受信したメッセージは、ハブに接続されているすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-375">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="2e727-376">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2e727-376">Test the app</span></span>

<span data-ttu-id="2e727-377">次の手順で、アプリの動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="2e727-377">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e727-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e727-378">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2e727-379">*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-379">Run Webpack in *release* mode.</span></span> <span data-ttu-id="2e727-380">**パッケージ マネージャー コンソール** ウィンドウを使用して、プロジェクト ルートで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-380">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="2e727-381">プロジェクト ルートにいない場合は、コマンドを入力する前に `cd SignalRWebPack` と入力します。</span><span class="sxs-lookup"><span data-stu-id="2e727-381">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2e727-382">**[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、デバッガーをアタッチせずに、ブラウザーでアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2e727-382">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="2e727-383">*wwroot/index.html* ファイルは `http://localhost:<port_number>` で提供されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-383">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="2e727-384">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="2e727-384">Open another browser instance (any browser).</span></span> <span data-ttu-id="2e727-385">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="2e727-385">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2e727-386">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e727-386">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2e727-387">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-387">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e727-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e727-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2e727-389">プロジェクト ルートで次のコマンドを実行して、*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-389">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2e727-390">プロジェクト ルートで次のコマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2e727-390">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="2e727-391">Web サーバーは、アプリを起動して、localhost 上で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="2e727-391">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="2e727-392">ブラウザーを開いて `http://localhost:<port_number>` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2e727-392">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="2e727-393">*wwwroot/index.html* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-393">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="2e727-394">アドレス バーから URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2e727-394">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="2e727-395">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="2e727-395">Open another browser instance (any browser).</span></span> <span data-ttu-id="2e727-396">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="2e727-396">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2e727-397">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e727-397">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2e727-398">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e727-398">The unique user name and message are displayed on both pages instantly.</span></span>

---

![両方のブラウザー ウィンドウに表示されるメッセージ](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2e727-400">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2e727-400">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
