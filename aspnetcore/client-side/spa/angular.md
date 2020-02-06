---
title: ASP.NET Core で Angular プロジェクト テンプレートを使用する
author: SteveSandersonMS
description: Angular と Angular CLI 用の ASP.NET Core シングル ページ アプリケーション (SPA) プロジェクト テンプレートの使用を開始する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: 11ad5d4c7cadcc582b3e288a331569f62f0b98ac
ms.sourcegitcommit: bd896935e91236e03241f75e6534ad6debcecbbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77044855"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="b228e-103">ASP.NET Core で Angular プロジェクト テンプレートを使用する</span><span class="sxs-lookup"><span data-stu-id="b228e-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="b228e-104">更新された Angular プロジェクト テンプレートは、Angular と Angular CLI を使用してリッチなクライアント側ユーザー インターフェイス (UI) を実装する ASP.NET Core アプリのための便利な開始点を提供します。</span><span class="sxs-lookup"><span data-stu-id="b228e-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="b228e-105">このテンプレートは、API バックエンドとして機能する ASP.NET Core プロジェクトと、UI として機能する Angular CLI プロジェクトの作成に相当します。</span><span class="sxs-lookup"><span data-stu-id="b228e-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="b228e-106">このテンプレートは、1 つのアプリ プロジェクトの中で、両方のプロジェクトの種類をホストするという利便性を提供します。</span><span class="sxs-lookup"><span data-stu-id="b228e-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="b228e-107">その結果、アプリ プロジェクトを 1 つのユニットとしてビルドして発行できます。</span><span class="sxs-lookup"><span data-stu-id="b228e-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="b228e-108">新しいアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b228e-108">Create a new app</span></span>

<span data-ttu-id="b228e-109">ASP.NET Core 2.1 がインストールされている場合は、Angular プロジェクト テンプレートをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b228e-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="b228e-110">コマンド プロンプトで `dotnet new angular` コマンドを使用して、空のディレクトリの中に新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b228e-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="b228e-111">たとえば、次のコマンドは、*my-new-app* ディレクトリにアプリを作成し、そのディレクトリに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="b228e-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="b228e-112">Visual Studio または .NET Core CLI からアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b228e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b228e-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="b228e-114">生成された *.csproj* ファイルを開き、そこから通常の方法でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="b228e-115">ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="b228e-116">以降のビルドは非常に高速になります。</span><span class="sxs-lookup"><span data-stu-id="b228e-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b228e-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b228e-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b228e-118">値が `ASPNETCORE_Environment` である `Development` という名前の環境変数があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b228e-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="b228e-119">Windows では (PowerShell ではないプロンプトで) `SET ASPNETCORE_Environment=Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="b228e-120">Linux または macOS では、`export ASPNETCORE_Environment=Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="b228e-121">[dotnet build](/dotnet/core/tools/dotnet-build) を実行して、アプリが正しくビルドされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b228e-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="b228e-122">ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="b228e-123">以降のビルドは非常に高速になります。</span><span class="sxs-lookup"><span data-stu-id="b228e-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="b228e-124">[dotnet run](/dotnet/core/tools/dotnet-run) を実行してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b228e-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="b228e-125">次のようなメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="b228e-126">ブラウザーでこの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="b228e-126">Navigate to this URL in a browser.</span></span>

<span data-ttu-id="b228e-127">アプリは、Angular CLI サーバーのインスタンスをバックグラウンドで開始します。</span><span class="sxs-lookup"><span data-stu-id="b228e-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="b228e-128">次のようなメッセージがログに記録されます。*NG Live Development Server is listening on localhost:&lt;otherport&gt;, open your browser on http://localhost:&lt;otherport&gt;/* 。</span><span class="sxs-lookup"><span data-stu-id="b228e-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open your browser on http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="b228e-129">このメッセージは無視してください。それは、結合された ASP.NET Core と Angular CLI アプリの URLでは&mdash;ありません \*\*。</span><span class="sxs-lookup"><span data-stu-id="b228e-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="b228e-130">プロジェクト テンプレートは、ASP.NET Core アプリと Angular アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b228e-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="b228e-131">ASP.NET Core アプリは、データ アクセス、承認、およびその他のサーバー側の操作で使用することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="b228e-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="b228e-132">*ClientApp* サブディレクトリ内の Angular アプリは、UI に関するすべての 操作で使用することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="b228e-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="b228e-133">ページ、画像、スタイル、モジュールなどを追加する</span><span class="sxs-lookup"><span data-stu-id="b228e-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="b228e-134">*ClientApp* ディレクトリには、標準的な Angular CLI アプリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b228e-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="b228e-135">詳細については、公式の [Angular ドキュメント](https://angular.io)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b228e-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="b228e-136">このテンプレートによって作成される Angular アプリと Angular CLI 自体によって (`ng new` で) 作成されるアプリにはわずかな違いがありますが、アプリの機能は変わりません。</span><span class="sxs-lookup"><span data-stu-id="b228e-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="b228e-137">テンプレートによって作成されるアプリには、[ブートストラップ](https://getbootstrap.com/)ベースのレイアウトと基本的なルーティングの例が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b228e-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="b228e-138">ng コマンドを実行する</span><span class="sxs-lookup"><span data-stu-id="b228e-138">Run ng commands</span></span>

<span data-ttu-id="b228e-139">コマンド プロンプトで、*ClientApp* サブディレクトリに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="b228e-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="b228e-140">`ng` ツールがグローバルにインストールされている場合は、そのコマンドのすべてを実行できます。</span><span class="sxs-lookup"><span data-stu-id="b228e-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="b228e-141">たとえば、`ng lint`、`ng test`、またはその他の [Angular CLI コマンド](https://angular.io/cli) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="b228e-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="b228e-142">ただし、`ng serve` を実行する必要はありません。これは、アプリのサーバー側とクライアント側の両方に関わる部分は、ASP.NET Core アプリが処理するためです。</span><span class="sxs-lookup"><span data-stu-id="b228e-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="b228e-143">内部的には、それは、開発時に `ng serve` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b228e-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="b228e-144">`ng` ツールがインストールされていない場合は、代わりに `npm run ng` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="b228e-145">たとえば、`npm run ng lint` または `npm run ng test` を実行できます。</span><span class="sxs-lookup"><span data-stu-id="b228e-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="b228e-146">npm パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="b228e-146">Install npm packages</span></span>

<span data-ttu-id="b228e-147">サードパーティ製の npm パッケージをインストールするには、*ClientApp* サブディレクトリでコマンド プロンプトを使用します。</span><span class="sxs-lookup"><span data-stu-id="b228e-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="b228e-148">例 :</span><span class="sxs-lookup"><span data-stu-id="b228e-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="b228e-149">発行と配置</span><span class="sxs-lookup"><span data-stu-id="b228e-149">Publish and deploy</span></span>

<span data-ttu-id="b228e-150">開発中、アプリは、開発者に便利なように最適化されたモードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="b228e-151">たとえば、JavaScript バンドルには、ソース マップが含まれます (デバッグ時に元の TypeScript コードを確認できるようにするためです)。</span><span class="sxs-lookup"><span data-stu-id="b228e-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="b228e-152">アプリは、ディスク上の TypeScript、HTML および CSS ファイルの変更を監視し、これらのファイルの変更が発生した場合は、再コンパイルと再読み込みを自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="b228e-153">運用時は、パフォーマンスが最適化されたバージョンのアプリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="b228e-154">これは、自動的に実行されるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="b228e-154">This is configured to happen automatically.</span></span> <span data-ttu-id="b228e-155">発行すると、ビルド構成によって、クライアント側コードの縮小された Ahead Of Time (AoT) コンパイルが行われたビルドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="b228e-156">開発ビルドとは異なり、運用ビルドでは、サーバーに node.js をインストールする必要はありません (サーバー側レンダリング (SSR) を有効にしている場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="b228e-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="b228e-157">標準的な [ASP.NET Core のホストと展開方法](xref:host-and-deploy/index)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b228e-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="b228e-158">"ng serve" を個別に実行する</span><span class="sxs-lookup"><span data-stu-id="b228e-158">Run "ng serve" independently</span></span>

<span data-ttu-id="b228e-159">ASP.NET Core アプリが開発モードで起動された場合、プロジェクトは、Angular CLI サーバーの独自のインスタンスをバックグラウンドで開始するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="b228e-160">これが便利なのは、別のサーバーを手動で実行する必要がないためです。</span><span class="sxs-lookup"><span data-stu-id="b228e-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="b228e-161">この既定の設定には欠点があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="b228e-162">C# コードを変更し、ASP.NET Core アプリを再起動する必要がある場合、Angular CLI サーバーが毎回再起動します。</span><span class="sxs-lookup"><span data-stu-id="b228e-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="b228e-163">再起動には、約 10 秒必要です。</span><span class="sxs-lookup"><span data-stu-id="b228e-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="b228e-164">C# コードを何度も編集するが、Angular CLI が再起動するまで待ちたくない場合は、ASP.NET Core プロセスから独立した Angular CLI サーバーを外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="b228e-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="b228e-165">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="b228e-165">To do so:</span></span>

1. <span data-ttu-id="b228e-166">コマンド プロンプトで、*ClientApp* サブディレクトリに切り替え、Angular CLI 開発サーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="b228e-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="b228e-167">Angular CLI 開発サーバーを起動するには、`npm start` ではなく `ng serve` を使用して、*package.json* の構成が使用されるようにします。</span><span class="sxs-lookup"><span data-stu-id="b228e-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="b228e-168">Angular CLI サーバーに追加パラメーターを渡すには、`scripts`package.json*ファイルの関連する* 行にそれらを追加します。</span><span class="sxs-lookup"><span data-stu-id="b228e-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="b228e-169">独自のインスタンスを起動する代わりに外部の Angular CLI インスタンスを使用するように ASP.NET Core アプリケーションを変更します。</span><span class="sxs-lookup"><span data-stu-id="b228e-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="b228e-170">*Startup* クラスで、`spa.UseAngularCliServer` の呼び出しを以下に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b228e-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="b228e-171">ASP.NET Core アプリの起動時に Angular CLI サーバーが起動されなくなります。</span><span class="sxs-lookup"><span data-stu-id="b228e-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="b228e-172">代わりに、手動で開始したインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="b228e-173">これにより、起動と再起動を高速化できます。</span><span class="sxs-lookup"><span data-stu-id="b228e-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="b228e-174">Angular CLI がクライアント アプリを毎回リビルドするまで待つ必要はなくなります。</span><span class="sxs-lookup"><span data-stu-id="b228e-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="b228e-175">.NET コードから TypeScript コードに データを渡す</span><span class="sxs-lookup"><span data-stu-id="b228e-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="b228e-176">SSR 中は、要求ごとのデータを ASP.NET Core アプリから Angular アプリに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b228e-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="b228e-177">たとえば、cookie 情報やデータベースから読み取ったデータを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b228e-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="b228e-178">これを行うには、*Startup* クラスを編集します。</span><span class="sxs-lookup"><span data-stu-id="b228e-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="b228e-179">`UseSpaPrerendering` のコールバックに、次のような `options.SupplyData` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="b228e-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="b228e-180">`SupplyData` コールバックでは、任意の要求ごとの JSON でシリアル可能なデータ (文字列、ブール値、数値など) を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b228e-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="b228e-181">*main.server.ts* コードは、これを `params.data` として受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b228e-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="b228e-182">たとえば、上記のコード サンプルは、ブール値を `params.data.isHttpsRequest` として `createServerRenderer` コールバックに渡しています。</span><span class="sxs-lookup"><span data-stu-id="b228e-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="b228e-183">Angular でサポートされている方法で、アプリの他の部分にこれを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b228e-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="b228e-184">たとえば、*main.server.ts* が、`BASE_URL` 値をコンストラクターが受け取ることを宣言されているコンポーネントにその値を渡す方法を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b228e-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="b228e-185">SSR の欠点</span><span class="sxs-lookup"><span data-stu-id="b228e-185">Drawbacks of SSR</span></span>

<span data-ttu-id="b228e-186">すべてのアプリが SSR から利益を得られるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="b228e-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="b228e-187">主な利益は、見かけ上のパフォーマンスです。</span><span class="sxs-lookup"><span data-stu-id="b228e-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="b228e-188">JavaScript バンドルのフェッチまたは解析にしばらく時間がかかる場合でも、低速のネットワーク接続または低速のモバイル デバイスでアプリに到達したユーザーに、初期 UI がすぐに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="b228e-189">ただし、多くの SPA は、主に高速の社内ネットワーク コンピューターで使用されているため、アプリはほぼ瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="b228e-190">同時に、SSR の有効化には重大な欠点があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="b228e-191">それは、開発プロセスの複雑さを深めます。</span><span class="sxs-lookup"><span data-stu-id="b228e-191">It adds complexity to your development process.</span></span> <span data-ttu-id="b228e-192">ASP.NET Core から呼び出される Node.js 環境の中で、クライアント側とサーバー側 という 2 つの異なる環境でコードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="b228e-193">考慮すべき点を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b228e-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="b228e-194">SSR では、運用サーバー上に Node.js をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="b228e-195">これは、Azure App Services などの一部のデプロイ シナリオでは自動的に実行されますが、Azure Service Fabric などの他のシナリオではそうではありません。</span><span class="sxs-lookup"><span data-stu-id="b228e-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="b228e-196">`BuildServerSideRenderer` ビルド フラグを有効にすると、*node_modules* ディレクトリが発行されます。</span><span class="sxs-lookup"><span data-stu-id="b228e-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="b228e-197">このフォルダーには、20,000 以上のファイルが含まれているため、配置時間が増加します。</span><span class="sxs-lookup"><span data-stu-id="b228e-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="b228e-198">Node.js 環境でコードを実行するために、`window` や `localStorage` などのブラウザー固有の JavaScript API の存在に頼ることはできません。</span><span class="sxs-lookup"><span data-stu-id="b228e-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="b228e-199">コード (または参照された一部のサードパーティ製ライブラリ) がこれらの API を使用しようとすると、SSR 中にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="b228e-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="b228e-200">たとえば、jQuery は多くの場所でブラウザー固有の API を参照するため、それを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b228e-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="b228e-201">エラーを防ぐには、SSR を有効にしないか、ブラウザー固有の API またはライブラリを使用しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b228e-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="b228e-202">SSR 中に呼び出されることがないように、このような API への呼び出しをチェックしてラップできます。</span><span class="sxs-lookup"><span data-stu-id="b228e-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="b228e-203">たとえば、JavaScript または TypeScript コードで、次のようなチェックを使用します。</span><span class="sxs-lookup"><span data-stu-id="b228e-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="b228e-204">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b228e-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
