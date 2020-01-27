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
ms.openlocfilehash: 9094a1d391c087a6f58aa9dd66e3697a79f4af86
ms.sourcegitcommit: ef1720cb733908f36a54825d84c3461c5280bdbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737517"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="e0d50-103">TypeScript と Webpack で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="e0d50-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="e0d50-104">作成者: [Sébastien Sougnez](https://twitter.com/ssougnez)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e0d50-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e0d50-105">[Webpack](https://webpack.js.org/) を使用すると、開発者は Web アプリのクライアント側のリソースをバンドルおよびビルドすることができます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="e0d50-106">このチュートリアルでは、クライアントが [TypeScript](https://www.typescriptlang.org/) で記述された ASP.NET Core SignalR Web アプリでの Webpack の使用法を示します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="e0d50-107">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e0d50-108">スターター ASP.NET Core SignalR アプリをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="e0d50-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="e0d50-109">SignalR TypeScript クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="e0d50-110">Webpack を使用してビルド パイプラインを構成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="e0d50-111">SignalR サーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="e0d50-112">クライアントとサーバー間の通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="e0d50-112">Enable communication between client and server</span></span>

<span data-ttu-id="e0d50-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e0d50-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e0d50-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e0d50-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0d50-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d50-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e0d50-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="e0d50-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="e0d50-117">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="e0d50-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="e0d50-118">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="e0d50-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0d50-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="e0d50-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="e0d50-121">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="e0d50-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="e0d50-122">C# for Visual Studio Code バージョン 1.17.1 またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="e0d50-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="e0d50-123">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="e0d50-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="e0d50-124">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0d50-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d50-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e0d50-126">*PATH* 環境変数で npm を検索するように Visual Studio を構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="e0d50-127">既定では、Visual Studio は、そのインストール ディレクトリ内で見つかった npm のバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="e0d50-128">Visual Studio のこれらの説明に従ってください。</span><span class="sxs-lookup"><span data-stu-id="e0d50-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="e0d50-129">**[ツール]** > **[オプション]** > **[プロジェクトとソリューション]** > **[Web パッケージ管理]** > **[外部 Web ツール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="e0d50-130">リストから *[$(PATH)]* エントリを選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="e0d50-131">上向き矢印をクリックして、エントリをリストの 2 番目の位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio の構成](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="e0d50-133">Visual Studio の構成が完了しました。</span><span class="sxs-lookup"><span data-stu-id="e0d50-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="e0d50-134">次はプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-134">It's time to create the project.</span></span>

1. <span data-ttu-id="e0d50-135">**[ファイル]** > **[新規]** > **[プロジェクト]** メニュー オプションを使用して、 **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="e0d50-136">プロジェクトに *SignalRWebPack* という名前を付け、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="e0d50-137">ターゲット フレームワークのドロップダウンから *[.NET Core]* を選択し、フレームワーク セレクターのドロップダウンから *[ASP.NET Core 3.0]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="e0d50-138">**空**のテンプレートを選択して、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0d50-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e0d50-140">**統合ターミナル**で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="e0d50-141">.NET Core をターゲットとする、空の ASP.NET Core Web アプリが *SignalRWebPack* ディレクトリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="e0d50-142">WebPack および TypeScript の構成</span><span class="sxs-lookup"><span data-stu-id="e0d50-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="e0d50-143">次の手順では、TypeScript の JavaScript への変換と、クライアント側のリソースのバンドルを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="e0d50-144">プロジェクト ルートで次のコマンドを実行して、*package.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="e0d50-145">強調表示されているプロパティを *package.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="e0d50-146">`private` プロパティを `true` に設定して、次の手順でパッケージのインストールの警告が表示されないようにします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="e0d50-147">必要な npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-147">Install the required npm packages.</span></span> <span data-ttu-id="e0d50-148">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="e0d50-149">注目するべきコマンドの詳細:</span><span class="sxs-lookup"><span data-stu-id="e0d50-149">Some command details to note:</span></span>

    * <span data-ttu-id="e0d50-150">バージョン番号は、各パッケージ名の `@` 記号の後に続きます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="e0d50-151">npm によって、これらの特定のパッケージ バージョンがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="e0d50-152">`-E` オプションは、[セマンティック バージョニング](https://semver.org/)範囲演算子を *package.json* に書き込む npm の既定の動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="e0d50-153">たとえば、`"webpack": "4.29.3"` が `"webpack": "^4.29.3"` の代わりに使用されています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="e0d50-154">このオプションにより、新しいパッケージ バージョンへの予期しないアップグレードが防止されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="e0d50-155">詳細については、[npm-install](https://docs.npmjs.com/cli/install) の公式ドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0d50-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="e0d50-156">*package.json* ファイルの `scripts` プロパティを次のスニペットで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="e0d50-157">スクリプトの説明 (一部):</span><span class="sxs-lookup"><span data-stu-id="e0d50-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="e0d50-158">`build`: 開発モードでクライアント側のリソースをバンドルし、ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="e0d50-159">ファイル監視により、プロジェクト ファイルを変更するたびにバンドルが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="e0d50-160">`mode` オプションは、ツリー シェイキングや縮小などの運用環境の最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="e0d50-161">`build` は開発でのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="e0d50-162">`release`: 運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="e0d50-163">`publish`:`release` スクリプトを実行して、運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="e0d50-164">.NET Core CLI の [publish](/dotnet/core/tools/dotnet-publish) コマンドを呼び出してアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="e0d50-165">プロジェクト ルートに、次の内容を持つ *webpack.config.js* という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="e0d50-166">上記のファイルは、Webpack コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="e0d50-167">注目するべき構成の詳細 (一部):</span><span class="sxs-lookup"><span data-stu-id="e0d50-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="e0d50-168">`output` プロパティにより、*dist* の既定値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="e0d50-169">代わりにバンドルが *wwwroot* ディレクトリ内に生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="e0d50-170">`resolve.extensions` 配列には、SignalR クライアント JavaScript をインポートするための *.js* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="e0d50-171">プロジェクト ルートに新しい *src* プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="e0d50-172">その目的は、プロジェクトのクライアント側アセットを格納することです。</span><span class="sxs-lookup"><span data-stu-id="e0d50-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="e0d50-173">次のコンテンツを含む *src/index.html* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="e0d50-174">上記の HTML では、ホームページの定型マークアップが定義されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="e0d50-175">新しい *src/css* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="e0d50-176">その目的は、プロジェクトの *.css* ファイルを格納することです。</span><span class="sxs-lookup"><span data-stu-id="e0d50-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="e0d50-177">次のコンテンツを含む *src/css/main.css* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="e0d50-178">上記の *main.css* ファイルは、アプリをスタイル設定します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="e0d50-179">次のコンテンツを含む *src/tsconfig.json* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="e0d50-180">上記のコードは、[ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 対応の JavaScript を生成するように TypeScript コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="e0d50-181">次のコンテンツを含む *src/index.ts* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="e0d50-182">上記の TypeScript は、DOM 要素への参照を取得し、次の 2 つのイベント ハンドラーをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="e0d50-183">`keyup`: ユーザーがテキスト ボックスに `tbMessage` として識別されるものを入力すると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="e0d50-184">ユーザーが **Enter** キーを押すと、`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="e0d50-185">`click`: ユーザーが **[送信]** ボタンをクリックすると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="e0d50-186">`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="e0d50-187">ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="e0d50-188">`Startup.Configure` メソッドで、[UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) および [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="e0d50-189">上記のコードにより、サーバーが *index.html* ファイルを見つけて提供することができます。ユーザーがファイルの完全な URL または Web アプリのルート URL を入力するかどうかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="e0d50-190">`Startup.Configure` メソッドの最後で、 */hub* ルートを `ChatHub` ハブにマップします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="e0d50-191">*Hello World!* を表示するコードを</span><span class="sxs-lookup"><span data-stu-id="e0d50-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="e0d50-192">次の行に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="e0d50-193">`Startup.ConfigureServices` メソッドで、[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="e0d50-194">これにより SignalR サービスがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="e0d50-195">プロジェクト ルートに *Hubs* という新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="e0d50-196">その目的は、次の手順で作成される SignalR ハブを格納することです。</span><span class="sxs-lookup"><span data-stu-id="e0d50-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="e0d50-197">次のコードを使用して、*Hubs/ChatHub.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="e0d50-198">`ChatHub` 参照を解決するには、次のコードを *Startup.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="e0d50-199">クライアントとサーバーの通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="e0d50-199">Enable client and server communication</span></span>

<span data-ttu-id="e0d50-200">アプリには現在、メッセージを送信するための単純なフォームが表示されています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="e0d50-201">送信しようとしても、何も起こりません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="e0d50-202">サーバーは特定のルートをリッスンしていますが、メッセージの送信については何も行いません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="e0d50-203">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="e0d50-204">上記のコマンドにより [SignalR TypeScript クライアント](https://www.npmjs.com/package/@microsoft/signalr) がインストールされ、クライアントがサーバーにメッセージを送信できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="e0d50-205">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="e0d50-206">上記のコードは、サーバーからのメッセージの受信をサポートします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="e0d50-207">`HubConnectionBuilder` クラスは、サーバー接続を構成するための新しいビルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="e0d50-208">`withUrl` 関数は、ハブ URL を構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-208">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="e0d50-209"> により、クライアントとサーバー間でのメッセージのやり取りが可能になります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-209"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="e0d50-210">各メッセージには特定の名前があります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-210">Each message has a specific name.</span></span> <span data-ttu-id="e0d50-211">たとえば、メッセージ ゾーンに新しいメッセージを表示するためのロジックを実行する `messageReceived` という名前を持つメッセージを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="e0d50-212">特定のメッセージをリッスンするには、`on` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="e0d50-213">任意の数のメッセージ名をリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-213">You can listen to any number of message names.</span></span> <span data-ttu-id="e0d50-214">作成者の名前や受信したメッセージの内容など、パラメーターをメッセージに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="e0d50-215">クライアントがメッセージを受信すると、`innerHTML` 属性に作成者の名前とメッセージ コンテンツを持つ新しい `div` 要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="e0d50-216">これはメッセージを表示する主要な `div` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="e0d50-217">これでクライアントがメッセージを受信できるようになったので、メッセージを送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="e0d50-218">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="e0d50-219">WebSocket 接続を介してメッセージを送信するには、`send` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="e0d50-220">メソッドの最初のパラメーターは、メッセージ名です。</span><span class="sxs-lookup"><span data-stu-id="e0d50-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="e0d50-221">メッセージ データは、他のパラメーターに存在しています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="e0d50-222">この例では、`newMessage` として識別されたメッセージがサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="e0d50-223">メッセージは、ユーザー名と、テキスト ボックスへのユーザー入力で構成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="e0d50-224">送信が機能していると、テキスト ボックスの値はクリアされます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="e0d50-225">強調表示されているメソッドを `ChatHub` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="e0d50-226">上記のコードは、サーバーがメッセージを受信すると、受信したメッセージをすべての接続されているユーザーにブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="e0d50-227">すべてのメッセージを受信するのに、ジェネリック `on` メソッドは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="e0d50-228">メソッドはメッセージ名のサフィックスに基づいて名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="e0d50-229">この例では、TypeScript クライアントが `newMessage` として識別されるメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="e0d50-230">C# `NewMessage` メソッドは、データがクライアントから送信されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="e0d50-231">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) で [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="e0d50-232">受信したメッセージは、ハブに接続されているすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="e0d50-233">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="e0d50-233">Test the app</span></span>

<span data-ttu-id="e0d50-234">次の手順で、アプリの動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0d50-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d50-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e0d50-236">*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="e0d50-237">**パッケージ マネージャー コンソール** ウィンドウを使用して、プロジェクト ルートで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="e0d50-238">プロジェクト ルートにいない場合は、コマンドを入力する前に `cd SignalRWebPack` と入力します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="e0d50-239">**[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、デバッガーをアタッチせずに、ブラウザーでアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="e0d50-240">*wwroot/index.html* ファイルは `http://localhost:<port_number>` で提供されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="e0d50-241">コンパイル エラーが発生した場合は、ソリューションを閉じてから再度開いてみてください。</span><span class="sxs-lookup"><span data-stu-id="e0d50-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="e0d50-242">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="e0d50-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="e0d50-243">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e0d50-244">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="e0d50-245">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0d50-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e0d50-247">プロジェクト ルートで次のコマンドを実行して、*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="e0d50-248">プロジェクト ルートで次のコマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="e0d50-249">Web サーバーは、アプリを起動して、localhost 上で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="e0d50-250">ブラウザーを開いて `http://localhost:<port_number>` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="e0d50-251">*wwwroot/index.html* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="e0d50-252">アドレス バーから URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="e0d50-253">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="e0d50-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="e0d50-254">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e0d50-255">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="e0d50-256">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![両方のブラウザー ウィンドウに表示されるメッセージ](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e0d50-258">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e0d50-258">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0d50-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d50-259">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e0d50-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="e0d50-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="e0d50-261">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="e0d50-261">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="e0d50-262">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="e0d50-262">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0d50-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-263">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="e0d50-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-264">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="e0d50-265">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="e0d50-265">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="e0d50-266">C# for Visual Studio Code バージョン 1.17.1 またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="e0d50-266">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="e0d50-267">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="e0d50-267">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="e0d50-268">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-268">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0d50-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d50-269">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e0d50-270">*PATH* 環境変数で npm を検索するように Visual Studio を構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-270">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="e0d50-271">既定では、Visual Studio は、そのインストール ディレクトリ内で見つかった npm のバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-271">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="e0d50-272">Visual Studio のこれらの説明に従ってください。</span><span class="sxs-lookup"><span data-stu-id="e0d50-272">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="e0d50-273">**[ツール]** > **[オプション]** > **[プロジェクトとソリューション]** > **[Web パッケージ管理]** > **[外部 Web ツール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-273">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="e0d50-274">リストから *[$(PATH)]* エントリを選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-274">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="e0d50-275">上向き矢印をクリックして、エントリをリストの 2 番目の位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-275">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio の構成](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="e0d50-277">Visual Studio の構成が完了しました。</span><span class="sxs-lookup"><span data-stu-id="e0d50-277">Visual Studio configuration is completed.</span></span> <span data-ttu-id="e0d50-278">次はプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-278">It's time to create the project.</span></span>

1. <span data-ttu-id="e0d50-279">**[ファイル]** > **[新規]** > **[プロジェクト]** メニュー オプションを使用して、 **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-279">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="e0d50-280">プロジェクトに *SignalRWebPack* という名前を付け、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-280">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="e0d50-281">ターゲット フレームワークのドロップダウンから、 *[.NET Core]* を選択し、フレームワーク セレクターのドロップダウンから *[ASP.NET Core 2.2]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-281">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="e0d50-282">**空**のテンプレートを選択して、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-282">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0d50-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e0d50-284">**統合ターミナル**で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-284">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="e0d50-285">.NET Core をターゲットとする、空の ASP.NET Core Web アプリが *SignalRWebPack* ディレクトリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-285">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="e0d50-286">WebPack および TypeScript の構成</span><span class="sxs-lookup"><span data-stu-id="e0d50-286">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="e0d50-287">次の手順では、TypeScript の JavaScript への変換と、クライアント側のリソースのバンドルを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-287">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="e0d50-288">プロジェクト ルートで次のコマンドを実行して、*package.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-288">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="e0d50-289">強調表示されているプロパティを *package.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-289">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="e0d50-290">`private` プロパティを `true` に設定して、次の手順でパッケージのインストールの警告が表示されないようにします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-290">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="e0d50-291">必要な npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-291">Install the required npm packages.</span></span> <span data-ttu-id="e0d50-292">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-292">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="e0d50-293">注目するべきコマンドの詳細:</span><span class="sxs-lookup"><span data-stu-id="e0d50-293">Some command details to note:</span></span>

    * <span data-ttu-id="e0d50-294">バージョン番号は、各パッケージ名の `@` 記号の後に続きます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-294">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="e0d50-295">npm によって、これらの特定のパッケージ バージョンがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-295">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="e0d50-296">`-E` オプションは、[セマンティック バージョニング](https://semver.org/)範囲演算子を *package.json* に書き込む npm の既定の動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-296">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="e0d50-297">たとえば、`"webpack": "4.29.3"` が `"webpack": "^4.29.3"` の代わりに使用されています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-297">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="e0d50-298">このオプションにより、新しいパッケージ バージョンへの予期しないアップグレードが防止されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-298">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="e0d50-299">詳細については、[npm-install](https://docs.npmjs.com/cli/install) の公式ドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0d50-299">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="e0d50-300">*package.json* ファイルの `scripts` プロパティを次のスニペットで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-300">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="e0d50-301">スクリプトの説明 (一部):</span><span class="sxs-lookup"><span data-stu-id="e0d50-301">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="e0d50-302">`build`: 開発モードでクライアント側のリソースをバンドルし、ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-302">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="e0d50-303">ファイル監視により、プロジェクト ファイルを変更するたびにバンドルが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-303">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="e0d50-304">`mode` オプションは、ツリー シェイキングや縮小などの運用環境の最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-304">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="e0d50-305">`build` は開発でのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-305">Only use `build` in development.</span></span>
    * <span data-ttu-id="e0d50-306">`release`: 運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-306">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="e0d50-307">`publish`:`release` スクリプトを実行して、運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-307">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="e0d50-308">.NET Core CLI の [publish](/dotnet/core/tools/dotnet-publish) コマンドを呼び出してアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-308">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="e0d50-309">プロジェクト ルートに、次の内容を持つ *webpack.config.js* という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-309">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="e0d50-310">上記のファイルは、Webpack コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-310">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="e0d50-311">注目するべき構成の詳細 (一部):</span><span class="sxs-lookup"><span data-stu-id="e0d50-311">Some configuration details to note:</span></span>

    * <span data-ttu-id="e0d50-312">`output` プロパティにより、*dist* の既定値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-312">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="e0d50-313">代わりにバンドルが *wwwroot* ディレクトリ内に生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-313">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="e0d50-314">`resolve.extensions` 配列には、SignalR クライアント JavaScript をインポートするための *.js* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-314">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="e0d50-315">プロジェクト ルートに新しい *src* プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-315">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="e0d50-316">その目的は、プロジェクトのクライアント側アセットを格納することです。</span><span class="sxs-lookup"><span data-stu-id="e0d50-316">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="e0d50-317">次のコンテンツを含む *src/index.html* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-317">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="e0d50-318">上記の HTML では、ホームページの定型マークアップが定義されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-318">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="e0d50-319">新しい *src/css* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-319">Create a new *src/css* directory.</span></span> <span data-ttu-id="e0d50-320">その目的は、プロジェクトの *.css* ファイルを格納することです。</span><span class="sxs-lookup"><span data-stu-id="e0d50-320">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="e0d50-321">次のコンテンツを含む *src/css/main.css* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-321">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="e0d50-322">上記の *main.css* ファイルは、アプリをスタイル設定します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-322">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="e0d50-323">次のコンテンツを含む *src/tsconfig.json* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-323">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="e0d50-324">上記のコードは、[ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 対応の JavaScript を生成するように TypeScript コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-324">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="e0d50-325">次のコンテンツを含む *src/index.ts* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-325">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="e0d50-326">上記の TypeScript は、DOM 要素への参照を取得し、次の 2 つのイベント ハンドラーをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-326">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="e0d50-327">`keyup`: ユーザーがテキスト ボックスに `tbMessage` として識別されるものを入力すると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-327">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="e0d50-328">ユーザーが **Enter** キーを押すと、`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-328">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="e0d50-329">`click`: ユーザーが **[送信]** ボタンをクリックすると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-329">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="e0d50-330">`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-330">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="e0d50-331">ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="e0d50-331">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="e0d50-332">`Startup.Configure` メソッドで提供されたコードにより、*Hello World!* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-332">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="e0d50-333">`app.Run` メソッド呼び出しを、[UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) と [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) の呼び出しで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-333">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="e0d50-334">上記のコードにより、サーバーが *index.html* ファイルを見つけて提供することができます。ユーザーがファイルの完全な URL または Web アプリのルート URL を入力するかどうかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-334">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="e0d50-335">`Startup.ConfigureServices` メソッドで [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-335">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e0d50-336">これにより SignalR サービスがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-336">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="e0d50-337">*/hub* ルートを `ChatHub` ハブにマップします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-337">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="e0d50-338">`Startup.Configure` メソッドの末尾に、次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-338">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="e0d50-339">プロジェクト ルートに *Hubs* という新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-339">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="e0d50-340">その目的は、次の手順で作成される SignalR ハブを格納することです。</span><span class="sxs-lookup"><span data-stu-id="e0d50-340">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="e0d50-341">次のコードを使用して、*Hubs/ChatHub.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-341">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="e0d50-342">`ChatHub` 参照を解決するには、次のコードを *Startup.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-342">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="e0d50-343">クライアントとサーバーの通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="e0d50-343">Enable client and server communication</span></span>

<span data-ttu-id="e0d50-344">アプリには現在、メッセージを送信するための単純なフォームが表示されています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-344">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="e0d50-345">送信しようとしても、何も起こりません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-345">Nothing happens when you try to do so.</span></span> <span data-ttu-id="e0d50-346">サーバーは特定のルートをリッスンしていますが、メッセージの送信については何も行いません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-346">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="e0d50-347">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-347">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="e0d50-348">上記のコマンドにより [SignalR TypeScript クライアント](https://www.npmjs.com/package/@microsoft/signalr) がインストールされ、クライアントがサーバーにメッセージを送信できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-348">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="e0d50-349">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-349">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="e0d50-350">上記のコードは、サーバーからのメッセージの受信をサポートします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-350">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="e0d50-351">`HubConnectionBuilder` クラスは、サーバー接続を構成するための新しいビルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-351">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="e0d50-352">`withUrl` 関数は、ハブ URL を構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-352">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="e0d50-353"> により、クライアントとサーバー間でのメッセージのやり取りが可能になります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-353"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="e0d50-354">各メッセージには特定の名前があります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-354">Each message has a specific name.</span></span> <span data-ttu-id="e0d50-355">たとえば、メッセージ ゾーンに新しいメッセージを表示するためのロジックを実行する `messageReceived` という名前を持つメッセージを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-355">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="e0d50-356">特定のメッセージをリッスンするには、`on` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-356">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="e0d50-357">任意の数のメッセージ名をリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-357">You can listen to any number of message names.</span></span> <span data-ttu-id="e0d50-358">作成者の名前や受信したメッセージの内容など、パラメーターをメッセージに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-358">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="e0d50-359">クライアントがメッセージを受信すると、`innerHTML` 属性に作成者の名前とメッセージ コンテンツを持つ新しい `div` 要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-359">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="e0d50-360">これはメッセージを表示する主要な `div` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-360">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="e0d50-361">これでクライアントがメッセージを受信できるようになったので、メッセージを送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-361">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="e0d50-362">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-362">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="e0d50-363">WebSocket 接続を介してメッセージを送信するには、`send` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0d50-363">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="e0d50-364">メソッドの最初のパラメーターは、メッセージ名です。</span><span class="sxs-lookup"><span data-stu-id="e0d50-364">The method's first parameter is the message name.</span></span> <span data-ttu-id="e0d50-365">メッセージ データは、他のパラメーターに存在しています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-365">The message data inhabits the other parameters.</span></span> <span data-ttu-id="e0d50-366">この例では、`newMessage` として識別されたメッセージがサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-366">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="e0d50-367">メッセージは、ユーザー名と、テキスト ボックスへのユーザー入力で構成されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-367">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="e0d50-368">送信が機能していると、テキスト ボックスの値はクリアされます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-368">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="e0d50-369">強調表示されているメソッドを `ChatHub` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-369">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="e0d50-370">上記のコードは、サーバーがメッセージを受信すると、受信したメッセージをすべての接続されているユーザーにブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-370">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="e0d50-371">すべてのメッセージを受信するのに、ジェネリック `on` メソッドは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e0d50-371">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="e0d50-372">メソッドはメッセージ名のサフィックスに基づいて名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-372">A method named after the message name suffices.</span></span>

    <span data-ttu-id="e0d50-373">この例では、TypeScript クライアントが `newMessage` として識別されるメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-373">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="e0d50-374">C# `NewMessage` メソッドは、データがクライアントから送信されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="e0d50-374">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="e0d50-375">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) で [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-375">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="e0d50-376">受信したメッセージは、ハブに接続されているすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-376">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="e0d50-377">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="e0d50-377">Test the app</span></span>

<span data-ttu-id="e0d50-378">次の手順で、アプリの動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-378">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0d50-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0d50-379">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e0d50-380">*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-380">Run Webpack in *release* mode.</span></span> <span data-ttu-id="e0d50-381">**パッケージ マネージャー コンソール** ウィンドウを使用して、プロジェクト ルートで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-381">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="e0d50-382">プロジェクト ルートにいない場合は、コマンドを入力する前に `cd SignalRWebPack` と入力します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-382">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="e0d50-383">**[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、デバッガーをアタッチせずに、ブラウザーでアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-383">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="e0d50-384">*wwroot/index.html* ファイルは `http://localhost:<port_number>` で提供されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-384">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="e0d50-385">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="e0d50-385">Open another browser instance (any browser).</span></span> <span data-ttu-id="e0d50-386">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-386">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e0d50-387">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-387">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="e0d50-388">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-388">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0d50-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0d50-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e0d50-390">プロジェクト ルートで次のコマンドを実行して、*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-390">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="e0d50-391">プロジェクト ルートで次のコマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="e0d50-391">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="e0d50-392">Web サーバーは、アプリを起動して、localhost 上で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-392">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="e0d50-393">ブラウザーを開いて `http://localhost:<port_number>` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-393">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="e0d50-394">*wwwroot/index.html* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-394">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="e0d50-395">アドレス バーから URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-395">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="e0d50-396">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="e0d50-396">Open another browser instance (any browser).</span></span> <span data-ttu-id="e0d50-397">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-397">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e0d50-398">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e0d50-398">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="e0d50-399">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0d50-399">The unique user name and message are displayed on both pages instantly.</span></span>

---

![両方のブラウザー ウィンドウに表示されるメッセージ](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e0d50-401">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e0d50-401">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
