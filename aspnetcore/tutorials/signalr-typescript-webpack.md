---
title: TypeScript と Webpack で ASP.NET Core SignalR を使用する
author: ssougnez
description: このチュートリアルでは、クライアントが TypeScript で記述された ASP.NET Core SignalR Web アプリをバンドルおよびビルドするために Webpack を構成します。
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511341"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="b2bea-103">TypeScript と Webpack で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="b2bea-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="b2bea-104">作成者: [Sébastien Sougnez](https://twitter.com/ssougnez)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b2bea-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b2bea-105">[Webpack](https://webpack.js.org/) を使用すると、開発者は Web アプリのクライアント側のリソースをバンドルおよびビルドすることができます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="b2bea-106">このチュートリアルでは、クライアントが [TypeScript](https://www.typescriptlang.org/) で記述された ASP.NET Core SignalR Web アプリでの Webpack の使用法を示します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="b2bea-107">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2bea-108">スターター ASP.NET Core SignalR アプリをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="b2bea-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="b2bea-109">SignalR TypeScript クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="b2bea-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="b2bea-110">Webpack を使用してビルド パイプラインを構成する</span><span class="sxs-lookup"><span data-stu-id="b2bea-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="b2bea-111">SignalR サーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="b2bea-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="b2bea-112">クライアントとサーバー間の通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="b2bea-112">Enable communication between client and server</span></span>

<span data-ttu-id="b2bea-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b2bea-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="b2bea-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b2bea-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2bea-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2bea-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2bea-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="b2bea-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="b2bea-117">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="b2bea-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="b2bea-118">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="b2bea-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2bea-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="b2bea-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="b2bea-121">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="b2bea-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b2bea-122">C# for Visual Studio Code バージョン 1.17.1 またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="b2bea-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="b2bea-123">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="b2bea-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="b2bea-124">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b2bea-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2bea-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2bea-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2bea-126">*PATH* 環境変数で npm を検索するように Visual Studio を構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="b2bea-127">既定では、Visual Studio は、そのインストール ディレクトリ内で見つかった npm のバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="b2bea-128">Visual Studio のこれらの説明に従ってください。</span><span class="sxs-lookup"><span data-stu-id="b2bea-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="b2bea-129">Visual Studio を起動します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-129">Launch Visual Studio.</span></span> <span data-ttu-id="b2bea-130">スタート ウィンドウで、 **[コードなしで続行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="b2bea-131">**[ツール]** > **[オプション]** > **[プロジェクトとソリューション]** > **[Web パッケージ管理]** > **[外部 Web ツール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="b2bea-132">リストから *[$(PATH)]* エントリを選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="b2bea-133">上向き矢印をクリックして、エントリをリストの 2 番目の位置に移動し、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Visual Studio の構成](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="b2bea-135">Visual Studio の構成が完了しました。</span><span class="sxs-lookup"><span data-stu-id="b2bea-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="b2bea-136">**[ファイル]**  >  **[新規]**  >  **[プロジェクト]** メニュー オプションを使用して、 **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="b2bea-137">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-137">Select **Next**.</span></span>
1. <span data-ttu-id="b2bea-138">プロジェクトに *SignalRWebPack* という名前を付け、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="b2bea-139">ターゲット フレームワークのドロップダウンから *[.NET Core]* を選択し、フレームワーク セレクターのドロップダウンから *[ASP.NET Core 3.1]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="b2bea-140">**空**のテンプレートを選択して、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="b2bea-141">`Microsoft.TypeScript.MSBuild` パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="b2bea-142">**ソリューション エクスプローラー** (右ペイン) でプロジェクト ノードを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="b2bea-143">**[参照]** タブで `Microsoft.TypeScript.MSBuild` を検索し、右側にある **[インストール]** をクリックしてパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="b2bea-144">Visual Studio によって**ソリューション エクスプローラー**の **[依存関係]** ノードの下に NuGet パッケージが追加され、プロジェクトで TypeScript のコンパイルができるようになります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2bea-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2bea-146">**統合ターミナル**で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="b2bea-147">`dotnet new` コマンドによって、空の ASP.NET Core Web アプリが "*SignalRWebPack*" ディレクトリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="b2bea-148">`code` コマンドは、Visual Studio Code の現在のインスタンス内で "*SignalRWebPack*" フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="b2bea-149">**統合ターミナル**で次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="b2bea-150">上記のコマンドにより、[Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) パッケージが追加され、プロジェクトでの TypeScript コンパイルが可能になります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="b2bea-151">WebPack および TypeScript の構成</span><span class="sxs-lookup"><span data-stu-id="b2bea-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="b2bea-152">次の手順では、TypeScript の JavaScript への変換と、クライアント側のリソースのバンドルを構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="b2bea-153">プロジェクト ルートで次のコマンドを実行して、"*package.json*" ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="b2bea-154">強調表示されているプロパティを "*package.json*" ファイルに追加し、ファイルの変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="b2bea-155">`private` プロパティを `true` に設定して、次の手順でパッケージのインストールの警告が表示されないようにします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="b2bea-156">必要な npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-156">Install the required npm packages.</span></span> <span data-ttu-id="b2bea-157">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="b2bea-158">注目するべきコマンドの詳細:</span><span class="sxs-lookup"><span data-stu-id="b2bea-158">Some command details to note:</span></span>

    * <span data-ttu-id="b2bea-159">バージョン番号は、各パッケージ名の `@` 記号の後に続きます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="b2bea-160">npm によって、これらの特定のパッケージ バージョンがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="b2bea-161">`-E` オプションは、[セマンティック バージョニング](https://semver.org/)範囲演算子を *package.json* に書き込む npm の既定の動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="b2bea-162">たとえば、`"webpack": "4.41.5"` が `"webpack": "^4.41.5"` の代わりに使用されています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="b2bea-163">このオプションにより、新しいパッケージ バージョンへの予期しないアップグレードが防止されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="b2bea-164">詳細については、[npm-install](https://docs.npmjs.com/cli/install) のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b2bea-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="b2bea-165">"*package.json*" ファイルの `scripts` プロパティを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="b2bea-166">スクリプトの説明 (一部):</span><span class="sxs-lookup"><span data-stu-id="b2bea-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="b2bea-167">`build`:開発モードでクライアント側のリソースをバンドルし、ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="b2bea-168">ファイル監視により、プロジェクト ファイルを変更するたびにバンドルが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="b2bea-169">`mode` オプションは、ツリー シェイキングや縮小などの運用環境の最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="b2bea-170">`build` は開発でのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="b2bea-171">`release`:運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="b2bea-172">`publish`:`release` スクリプトを実行して、運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="b2bea-173">.NET Core CLI の [publish](/dotnet/core/tools/dotnet-publish) コマンドを呼び出してアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="b2bea-174">プロジェクト ルートに、次のコードを含む "*webpack.config.js*" という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="b2bea-175">上記のファイルは、Webpack コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="b2bea-176">注目するべき構成の詳細 (一部):</span><span class="sxs-lookup"><span data-stu-id="b2bea-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="b2bea-177">`output` プロパティにより、*dist* の既定値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="b2bea-178">代わりにバンドルが *wwwroot* ディレクトリ内に生成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="b2bea-179">`resolve.extensions` 配列には、SignalR クライアント JavaScript をインポートするための *.js* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="b2bea-180">プロジェクトのクライアント側アセットを格納するために、プロジェクト ルートに新しい "*src*" ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="b2bea-181">次のマークアップを含む "*src/index.html*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="b2bea-182">上記の HTML では、ホームページの定型マークアップが定義されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="b2bea-183">新しい *src/css* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="b2bea-184">その目的は、プロジェクトの *.css* ファイルを格納することです。</span><span class="sxs-lookup"><span data-stu-id="b2bea-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="b2bea-185">次の CSS を含む "*src/css/main.css*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="b2bea-186">上記の *main.css* ファイルは、アプリをスタイル設定します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="b2bea-187">次の JSON を含む "*src/tsconfig.json*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="b2bea-188">上記のコードは、[ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 対応の JavaScript を生成するように TypeScript コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="b2bea-189">次のコードを含む "*src/index.ts*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="b2bea-190">上記の TypeScript は、DOM 要素への参照を取得し、次の 2 つのイベント ハンドラーをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="b2bea-191">`keyup`:ユーザーが `tbMessage` テキスト ボックスに入力すると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="b2bea-192">ユーザーが **Enter** キーを押すと、`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="b2bea-193">`click`: ユーザーが **[送信]** ボタンをクリックすると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="b2bea-194">`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="b2bea-195">Configure the app</span><span class="sxs-lookup"><span data-stu-id="b2bea-195">Configure the app</span></span>

1. <span data-ttu-id="b2bea-196">`Startup.Configure` で、[UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) および [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="b2bea-197">上記のコードを使用すると、サーバーで "*index.html*" ファイルを検索して提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="b2bea-198">ファイルは、ユーザーが Web アプリの完全な URL またはルート URL を入力した場合に提供されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="b2bea-199">`Startup.Configure` の最後で、" */hub*" ルートを `ChatHub` ハブにマップします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="b2bea-200">*Hello World!* を表示するコードを</span><span class="sxs-lookup"><span data-stu-id="b2bea-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="b2bea-201">次の行に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="b2bea-202">`Startup.ConfigureServices` で、[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="b2bea-203">SignalR ハブを格納するために、プロジェクトルート "*SignalRWebPack/* " に "*Hubs*" という名前の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="b2bea-204">次のコードを使用して、*Hubs/ChatHub.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="b2bea-205">`ChatHub` 参照を解決するには、次の `using` ステートメントを "*Startup.cs*" ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="b2bea-206">クライアントとサーバーの通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="b2bea-206">Enable client and server communication</span></span>

<span data-ttu-id="b2bea-207">現在、アプリにはメッセージを送信するための基本フォームが表示されていますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="b2bea-208">サーバーは特定のルートをリッスンしていますが、メッセージの送信については何も行いません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="b2bea-209">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="b2bea-210">上記のコマンドにより、次がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-210">The preceding command installs:</span></span>

     * <span data-ttu-id="b2bea-211">[SignalR TypeScript クライアント](https://www.npmjs.com/package/@microsoft/signalr)。クライアントがサーバーにメッセージを送信できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="b2bea-212">Node.js の TypeScript 型定義。Node.js 型のコンパイル時チェックが有効になります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="b2bea-213">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="b2bea-214">上記のコードは、サーバーからのメッセージの受信をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="b2bea-215">`HubConnectionBuilder` クラスは、サーバー接続を構成するための新しいビルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="b2bea-216">`withUrl` 関数は、ハブ URL を構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="b2bea-217">SignalR により、クライアントとサーバー間でのメッセージのやり取りが可能になります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="b2bea-218">各メッセージには特定の名前があります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-218">Each message has a specific name.</span></span> <span data-ttu-id="b2bea-219">たとえば、`messageReceived` という名前のメッセージは、メッセージ ゾーンに新しいメッセージを表示するためのロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="b2bea-220">特定のメッセージをリッスンするには、`on` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="b2bea-221">任意の数のメッセージ名をリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="b2bea-222">作成者の名前や受信したメッセージの内容など、パラメーターをメッセージに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="b2bea-223">クライアントがメッセージを受信すると、`innerHTML` 属性に作成者の名前とメッセージ コンテンツを持つ新しい `div` 要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="b2bea-224">これはメッセージを表示する主要な `div` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="b2bea-225">これでクライアントがメッセージを受信できるようになったので、メッセージを送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="b2bea-226">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="b2bea-227">WebSocket 接続を介してメッセージを送信するには、`send` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="b2bea-228">メソッドの最初のパラメーターは、メッセージ名です。</span><span class="sxs-lookup"><span data-stu-id="b2bea-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="b2bea-229">メッセージ データは、他のパラメーターに存在しています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="b2bea-230">この例では、`newMessage` として識別されたメッセージがサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="b2bea-231">メッセージは、ユーザー名と、テキスト ボックスへのユーザー入力で構成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="b2bea-232">送信が機能していると、テキスト ボックスの値はクリアされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="b2bea-233">`NewMessage` メソッドを `ChatHub` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="b2bea-234">上記のコードは、サーバーがメッセージを受信すると、受信したメッセージをすべての接続されているユーザーにブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="b2bea-235">すべてのメッセージを受信するのに、ジェネリック `on` メソッドは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="b2bea-236">メソッドはメッセージ名のサフィックスに基づいて名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="b2bea-237">この例では、TypeScript クライアントが `newMessage` として識別されるメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="b2bea-238">C# `NewMessage` メソッドは、データがクライアントから送信されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="b2bea-239">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) で [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="b2bea-240">受信したメッセージは、ハブに接続されているすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="b2bea-241">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b2bea-241">Test the app</span></span>

<span data-ttu-id="b2bea-242">次の手順で、アプリの動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2bea-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2bea-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b2bea-244">*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="b2bea-245">**パッケージ マネージャー コンソール** ウィンドウを使用して、プロジェクト ルートで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="b2bea-246">プロジェクト ルートにいない場合は、コマンドを入力する前に `cd SignalRWebPack` と入力します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="b2bea-247">**[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、デバッガーをアタッチせずに、ブラウザーでアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="b2bea-248">*wwroot/index.html* ファイルは `http://localhost:<port_number>` で提供されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="b2bea-249">コンパイル エラーが発生した場合は、ソリューションを閉じてから再度開いてみてください。</span><span class="sxs-lookup"><span data-stu-id="b2bea-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="b2bea-250">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="b2bea-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="b2bea-251">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b2bea-252">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="b2bea-253">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2bea-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b2bea-255">プロジェクト ルートで次のコマンドを実行して、*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="b2bea-256">プロジェクト ルートで次のコマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="b2bea-257">Web サーバーは、アプリを起動して、localhost 上で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="b2bea-258">ブラウザーを開いて `http://localhost:<port_number>` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="b2bea-259">*wwwroot/index.html* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="b2bea-260">アドレス バーから URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="b2bea-261">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="b2bea-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="b2bea-262">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b2bea-263">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="b2bea-264">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![両方のブラウザー ウィンドウに表示されるメッセージ](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="b2bea-266">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b2bea-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2bea-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2bea-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2bea-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="b2bea-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="b2bea-269">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="b2bea-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="b2bea-270">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="b2bea-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2bea-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="b2bea-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="b2bea-273">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="b2bea-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b2bea-274">C# for Visual Studio Code バージョン 1.17.1 またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="b2bea-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="b2bea-275">[Node.js](https://nodejs.org/) ([npm](https://www.npmjs.com/) 使用)</span><span class="sxs-lookup"><span data-stu-id="b2bea-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="b2bea-276">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b2bea-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2bea-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2bea-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2bea-278">*PATH* 環境変数で npm を検索するように Visual Studio を構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="b2bea-279">既定では、Visual Studio は、そのインストール ディレクトリ内で見つかった npm のバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="b2bea-280">Visual Studio のこれらの説明に従ってください。</span><span class="sxs-lookup"><span data-stu-id="b2bea-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="b2bea-281">**[ツール]** > **[オプション]** > **[プロジェクトとソリューション]** > **[Web パッケージ管理]** > **[外部 Web ツール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="b2bea-282">リストから *[$(PATH)]* エントリを選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="b2bea-283">上向き矢印をクリックして、エントリをリストの 2 番目の位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio の構成](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="b2bea-285">Visual Studio の構成が完了しました。</span><span class="sxs-lookup"><span data-stu-id="b2bea-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="b2bea-286">次はプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-286">It's time to create the project.</span></span>

1. <span data-ttu-id="b2bea-287">**[ファイル]** > **[新規]** > **[プロジェクト]** メニュー オプションを使用して、 **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="b2bea-288">プロジェクトに *SignalRWebPack* という名前を付け、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="b2bea-289">ターゲット フレームワークのドロップダウンから、 *[.NET Core]* を選択し、フレームワーク セレクターのドロップダウンから *[ASP.NET Core 2.2]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="b2bea-290">**空**のテンプレートを選択して、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2bea-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2bea-292">**統合ターミナル**で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="b2bea-293">.NET Core をターゲットとする、空の ASP.NET Core Web アプリが *SignalRWebPack* ディレクトリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="b2bea-294">WebPack および TypeScript の構成</span><span class="sxs-lookup"><span data-stu-id="b2bea-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="b2bea-295">次の手順では、TypeScript の JavaScript への変換と、クライアント側のリソースのバンドルを構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="b2bea-296">プロジェクト ルートで次のコマンドを実行して、"*package.json*" ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="b2bea-297">強調表示されているプロパティを *package.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="b2bea-298">`private` プロパティを `true` に設定して、次の手順でパッケージのインストールの警告が表示されないようにします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="b2bea-299">必要な npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-299">Install the required npm packages.</span></span> <span data-ttu-id="b2bea-300">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="b2bea-301">注目するべきコマンドの詳細:</span><span class="sxs-lookup"><span data-stu-id="b2bea-301">Some command details to note:</span></span>

    * <span data-ttu-id="b2bea-302">バージョン番号は、各パッケージ名の `@` 記号の後に続きます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="b2bea-303">npm によって、これらの特定のパッケージ バージョンがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="b2bea-304">`-E` オプションは、[セマンティック バージョニング](https://semver.org/)範囲演算子を *package.json* に書き込む npm の既定の動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="b2bea-305">たとえば、`"webpack": "4.29.3"` が `"webpack": "^4.29.3"` の代わりに使用されています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="b2bea-306">このオプションにより、新しいパッケージ バージョンへの予期しないアップグレードが防止されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="b2bea-307">詳細については、[npm-install](https://docs.npmjs.com/cli/install) のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b2bea-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="b2bea-308">"*package.json*" ファイルの `scripts` プロパティを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="b2bea-309">スクリプトの説明 (一部):</span><span class="sxs-lookup"><span data-stu-id="b2bea-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="b2bea-310">`build`:開発モードでクライアント側のリソースをバンドルし、ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="b2bea-311">ファイル監視により、プロジェクト ファイルを変更するたびにバンドルが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="b2bea-312">`mode` オプションは、ツリー シェイキングや縮小などの運用環境の最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="b2bea-313">`build` は開発でのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="b2bea-314">`release`:運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="b2bea-315">`publish`:`release` スクリプトを実行して、運用モードでクライアント側のリソースをバンドルします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="b2bea-316">.NET Core CLI の [publish](/dotnet/core/tools/dotnet-publish) コマンドを呼び出してアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="b2bea-317">プロジェクト ルートに、次のコードを含む "*webpack.config.js*" という名前のファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="b2bea-318">上記のファイルは、Webpack コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="b2bea-319">注目するべき構成の詳細 (一部):</span><span class="sxs-lookup"><span data-stu-id="b2bea-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="b2bea-320">`output` プロパティにより、*dist* の既定値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="b2bea-321">代わりにバンドルが *wwwroot* ディレクトリ内に生成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="b2bea-322">`resolve.extensions` 配列には、SignalR クライアント JavaScript をインポートするための *.js* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="b2bea-323">プロジェクトのクライアント側アセットを格納するために、プロジェクト ルートに新しい "*src*" ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="b2bea-324">次のマークアップを含む "*src/index.html*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="b2bea-325">上記の HTML では、ホームページの定型マークアップが定義されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="b2bea-326">新しい *src/css* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="b2bea-327">その目的は、プロジェクトの *.css* ファイルを格納することです。</span><span class="sxs-lookup"><span data-stu-id="b2bea-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="b2bea-328">次のマークアップを含む "*src/css/main.css*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="b2bea-329">上記の *main.css* ファイルは、アプリをスタイル設定します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="b2bea-330">次の JSON を含む "*src/tsconfig.json*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="b2bea-331">上記のコードは、[ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 対応の JavaScript を生成するように TypeScript コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="b2bea-332">次のコードを含む "*src/index.ts*" を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="b2bea-333">上記の TypeScript は、DOM 要素への参照を取得し、次の 2 つのイベント ハンドラーをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="b2bea-334">`keyup`:ユーザーが `tbMessage` テキスト ボックスに入力すると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="b2bea-335">ユーザーが **Enter** キーを押すと、`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="b2bea-336">`click`: ユーザーが **[送信]** ボタンをクリックすると、このイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="b2bea-337">`send` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="b2bea-338">ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="b2bea-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="b2bea-339">`Startup.Configure` メソッドで提供されたコードにより、*Hello World!* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="b2bea-340">`app.Run` メソッド呼び出しを、[UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) と [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) の呼び出しで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="b2bea-341">上記のコードにより、サーバーが *index.html* ファイルを見つけて提供することができます。ユーザーがファイルの完全な URL または Web アプリのルート URL を入力するかどうかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="b2bea-342">`Startup.ConfigureServices` で、[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b2bea-343">これにより SignalR サービスがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="b2bea-344">*/hub* ルートを `ChatHub` ハブにマップします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="b2bea-345">`Startup.Configure` の末尾に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="b2bea-346">プロジェクト ルートに *Hubs* という新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="b2bea-347">その目的は、次の手順で作成される SignalR ハブを格納することです。</span><span class="sxs-lookup"><span data-stu-id="b2bea-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="b2bea-348">次のコードを使用して、*Hubs/ChatHub.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="b2bea-349">`ChatHub` 参照を解決するには、次のコードを *Startup.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="b2bea-350">クライアントとサーバーの通信を有効にする</span><span class="sxs-lookup"><span data-stu-id="b2bea-350">Enable client and server communication</span></span>

<span data-ttu-id="b2bea-351">アプリには現在、メッセージを送信するための単純なフォームが表示されています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="b2bea-352">送信しようとしても、何も起こりません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="b2bea-353">サーバーは特定のルートをリッスンしていますが、メッセージの送信については何も行いません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="b2bea-354">プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="b2bea-355">上記のコマンドにより [SignalR TypeScript クライアント](https://www.npmjs.com/package/@microsoft/signalr) がインストールされ、クライアントがサーバーにメッセージを送信できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="b2bea-356">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="b2bea-357">上記のコードは、サーバーからのメッセージの受信をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="b2bea-358">`HubConnectionBuilder` クラスは、サーバー接続を構成するための新しいビルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="b2bea-359">`withUrl` 関数は、ハブ URL を構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="b2bea-360">SignalR により、クライアントとサーバー間でのメッセージのやり取りが可能になります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="b2bea-361">各メッセージには特定の名前があります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-361">Each message has a specific name.</span></span> <span data-ttu-id="b2bea-362">たとえば、`messageReceived` という名前のメッセージは、メッセージ ゾーンに新しいメッセージを表示するためのロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="b2bea-363">特定のメッセージをリッスンするには、`on` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="b2bea-364">任意の数のメッセージ名をリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-364">You can listen to any number of message names.</span></span> <span data-ttu-id="b2bea-365">作成者の名前や受信したメッセージの内容など、パラメーターをメッセージに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="b2bea-366">クライアントがメッセージを受信すると、`innerHTML` 属性に作成者の名前とメッセージ コンテンツを持つ新しい `div` 要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="b2bea-367">新しいメッセージが、メッセージを表示する主要な `div` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="b2bea-368">これでクライアントがメッセージを受信できるようになったので、メッセージを送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="b2bea-369">強調表示されたコードを *src/index.ts* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="b2bea-370">WebSocket 接続を介してメッセージを送信するには、`send` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2bea-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="b2bea-371">メソッドの最初のパラメーターは、メッセージ名です。</span><span class="sxs-lookup"><span data-stu-id="b2bea-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="b2bea-372">メッセージ データは、他のパラメーターに存在しています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="b2bea-373">この例では、`newMessage` として識別されたメッセージがサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="b2bea-374">メッセージは、ユーザー名と、テキスト ボックスへのユーザー入力で構成されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="b2bea-375">送信が機能していると、テキスト ボックスの値はクリアされます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="b2bea-376">`NewMessage` メソッドを `ChatHub` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="b2bea-377">上記のコードは、サーバーがメッセージを受信すると、受信したメッセージをすべての接続されているユーザーにブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="b2bea-378">すべてのメッセージを受信するのに、ジェネリック `on` メソッドは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b2bea-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="b2bea-379">メソッドはメッセージ名のサフィックスに基づいて名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="b2bea-380">この例では、TypeScript クライアントが `newMessage` として識別されるメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="b2bea-381">C# `NewMessage` メソッドは、データがクライアントから送信されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="b2bea-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="b2bea-382">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) で [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="b2bea-383">受信したメッセージは、ハブに接続されているすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="b2bea-384">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b2bea-384">Test the app</span></span>

<span data-ttu-id="b2bea-385">次の手順で、アプリの動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2bea-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2bea-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b2bea-387">*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="b2bea-388">**パッケージ マネージャー コンソール** ウィンドウを使用して、プロジェクト ルートで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="b2bea-389">プロジェクト ルートにいない場合は、コマンドを入力する前に `cd SignalRWebPack` と入力します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="b2bea-390">**[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、デバッガーをアタッチせずに、ブラウザーでアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="b2bea-391">*wwroot/index.html* ファイルは `http://localhost:<port_number>` で提供されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="b2bea-392">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="b2bea-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="b2bea-393">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b2bea-394">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="b2bea-395">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2bea-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2bea-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b2bea-397">プロジェクト ルートで次のコマンドを実行して、*リリース* モードで Webpack を実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="b2bea-398">プロジェクト ルートで次のコマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="b2bea-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="b2bea-399">Web サーバーは、アプリを起動して、localhost 上で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="b2bea-400">ブラウザーを開いて `http://localhost:<port_number>` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="b2bea-401">*wwwroot/index.html* ファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="b2bea-402">アドレス バーから URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="b2bea-403">別のブラウザー インスタンスを開きます (任意のブラウザー)。</span><span class="sxs-lookup"><span data-stu-id="b2bea-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="b2bea-404">アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b2bea-405">いずれかのブラウザーを選択し、 **[メッセージ]** テキスト ボックスにメッセージを入力し、 **[送信]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b2bea-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="b2bea-406">次の瞬間、両方のページに一意のユーザー名とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b2bea-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![両方のブラウザー ウィンドウに表示されるメッセージ](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b2bea-408">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b2bea-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
