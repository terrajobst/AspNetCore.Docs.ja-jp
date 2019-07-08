---
title: クライアント側の ASP.NET Core Blazor をホストして展開する
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストし展開する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 46c99364098557557bff0c38cab5a91ee2d3979b
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538644"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a><span data-ttu-id="6b50d-103">クライアント側の ASP.NET Core Blazor をホストして展開する</span><span class="sxs-lookup"><span data-stu-id="6b50d-103">Host and deploy ASP.NET Core Blazor client-side</span></span>

<span data-ttu-id="6b50d-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6b50d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6b50d-105">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="6b50d-105">Host configuration values</span></span>

<span data-ttu-id="6b50d-106">[クライアント側のホスティング モデル](xref:blazor/hosting-models#client-side) を使用する Blazor アプリでは、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="6b50d-107">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="6b50d-107">Content Root</span></span>

<span data-ttu-id="6b50d-108">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="6b50d-109">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="6b50d-110">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6b50d-111">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="6b50d-112">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6b50d-113">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="6b50d-114">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6b50d-115">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="6b50d-116">パス ベース</span><span class="sxs-lookup"><span data-stu-id="6b50d-116">Path base</span></span>

<span data-ttu-id="6b50d-117">`--pathbase` 引数により、ルート以外の仮想パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="6b50d-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="6b50d-118">次の例では、`/virtual-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="6b50d-119">詳細については、「[App base path](#app-base-path)」(アプリのベース パス) セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6b50d-120">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="6b50d-121">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="6b50d-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="6b50d-122">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6b50d-123">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="6b50d-124">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6b50d-125">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="6b50d-126">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6b50d-127">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="6b50d-128">URL</span><span class="sxs-lookup"><span data-stu-id="6b50d-128">URLs</span></span>

<span data-ttu-id="6b50d-129">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="6b50d-130">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6b50d-131">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="6b50d-132">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6b50d-133">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="6b50d-134">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6b50d-135">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="6b50d-136">配置</span><span class="sxs-lookup"><span data-stu-id="6b50d-136">Deployment</span></span>

<span data-ttu-id="6b50d-137">[クライアント側のホスティング モデルは次のとおりです](xref:blazor/hosting-models#client-side)。</span><span class="sxs-lookup"><span data-stu-id="6b50d-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="6b50d-138">Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="6b50d-139">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="6b50d-140">次の方法のいずれかがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6b50d-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="6b50d-141">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="6b50d-142">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="6b50d-143">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="6b50d-144">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="6b50d-145">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="6b50d-145">Configure the Linker</span></span>

<span data-ttu-id="6b50d-146">Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="6b50d-147">アセンブリのリンクはビルドで制御できます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="6b50d-148">詳細については、<xref:host-and-deploy/blazor/configure-linker> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="6b50d-149">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="6b50d-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="6b50d-150">クライアント側のアプリ内のページ コンポーネントに対するルーティング要求は、サーバー側のホストされているアプリに対するルーティング要求のようにシンプルなものではありません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="6b50d-151">次の 2 つのコンポーネントがあるクライアント側のアプリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-151">Consider a client-side app with two components:</span></span>

* <span data-ttu-id="6b50d-152">*Main.razor* &ndash; アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b50d-152">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="6b50d-153">*About.razor* &ndash; `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="6b50d-153">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="6b50d-154">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="6b50d-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="6b50d-155">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-155">The browser makes a request.</span></span>
1. <span data-ttu-id="6b50d-156">既定のページ (通常は *index.html*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="6b50d-157">*index.html* によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="6b50d-158">Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-158">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="6b50d-159">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-159">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="6b50d-160">"*クライアント側アプリ内にある*" 内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-160">All of the requests for internal endpoints *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="6b50d-161">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-161">The router handles the requests internally.</span></span>

<span data-ttu-id="6b50d-162">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-162">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="6b50d-163">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-163">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="6b50d-164">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-164">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="6b50d-165">*index.html* が返されると、アプリのクライアント側のルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-165">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="6b50d-166">アプリのベース パス</span><span class="sxs-lookup"><span data-stu-id="6b50d-166">App base path</span></span>

<span data-ttu-id="6b50d-167">"*アプリのベース パス*" は、サーバー上の仮想アプリ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-167">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="6b50d-168">例えば、`/CoolApp/` の仮想フォルダー内の Contoso サーバー上に存在するアプリの場合、そのアプリは `https://www.contoso.com/CoolApp` で到達可能であり、仮想ベース パスは `/CoolApp/` となります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-168">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="6b50d-169">アプリのベース パスを仮想パス (`<base href="/CoolApp/">`) に設定することで、アプリのサーバー上の仮想位置がわかります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-169">By setting the app base path to the virtual path (`<base href="/CoolApp/">`), the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="6b50d-170">アプリでは、アプリのベース パスを使用して、ルート ディレクトリに存在しないコンポーネントからのアプリのルートへの相対 URL を構築することができます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-170">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="6b50d-171">これにより、ディレクトリ構造の別のレベルに存在するコンポーネントでは、アプリ内のさまざまな場所にある他のリソースに対するリンクを構築することができます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-171">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="6b50d-172">リンクの `href` ターゲットがアプリのベース パス URI 空間内の場合に、そのハイパーリンクのクリックを阻止するためにも使用できます。つまり、Blazor のルーターにより内部ナビゲーションが処理されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-172">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="6b50d-173">多くのホスティング シナリオでは、サーバーのアプリへの仮想パスは、アプリのルートです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-173">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="6b50d-174">このような場合、アプリのベース パスは最初にスラッシュ (`<base href="/" />`) が付きます。これは、アプリの既定の構成です。</span><span class="sxs-lookup"><span data-stu-id="6b50d-174">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="6b50d-175">GitHub ページと IIS 仮想ディレクトリまたはサブアプリケーションなど、その他のホスティング シナリオの場合、サーバーのアプリへの仮想パスを、アプリのベース パスとして設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-175">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="6b50d-176">アプリのベース パスを設定するには、*wwwroot/index.html* ファイルの `<head>` タグ要素内の `<base>` タグを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-176">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="6b50d-177">`href` 属性値として `/virtual-path/` (末尾にスラッシュが必要) を設定します。ここで、`/virtual-path/` は、アプリに対するサーバー上の完全仮想アプリ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-177">Set the `href` attribute value to `/virtual-path/` (the trailing slash is required), where `/virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="6b50d-178">前の例では、仮想パスには `/CoolApp/`: `<base href="/CoolApp/">` が設定されていました。</span><span class="sxs-lookup"><span data-stu-id="6b50d-178">In the preceding example, the virtual path is set to `/CoolApp/`: `<base href="/CoolApp/">`.</span></span>

<span data-ttu-id="6b50d-179">ルート以外の仮想パスが構成されているアプリの場合 (例: `<base href="/CoolApp/">`)、そのアプリは*ローカルで実行*されていると自身のリソースを見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-179">For an app with a non-root virtual path configured (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="6b50d-180">ローカルでの開発およびテスト中は、実行時の `<base>` タグの `href` 値と一致する*パス ベース*引数を指定することで、この問題を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-180">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="6b50d-181">アプリをローカルで実行しているときにパス ベース引数をルート パス (`/`) と共に渡すには、アプリのディレクトリから `--pathbase` オプションを指定して `dotnet run` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-181">To pass the path base argument with the root path (`/`) when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

<span data-ttu-id="6b50d-182">仮想ベース パスが `/CoolApp/` (`<base href="/CoolApp/">`) のアプリについては、コマンドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-182">For an app with a virtual base path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="6b50d-183">アプリは `http://localhost:port/CoolApp` でローカルで応答します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="6b50d-184">詳しくは、[パス ベースのホスト構成値](#path-base)に関するセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="6b50d-185">アプリが[クライアント側のホスティング モデル](xref:blazor/hosting-models#client-side) (**Blazor (クライアント側)** プロジェクト テンプレートに基づく。[dotnet new](/dotnet/core/tools/dotnet-new) コマンドの使用時は `blazor` テンプレート) を使用し、ASP.NET Core アプリ内で IIS サブアプリとしてホストされている場合、継承された ASP.NET Core モジュール ハンドラーを無効とするか、*web.config* ファイル内のルート (親) アプリの `<handlers>` セクションがサブアプリに継承されていないことを確認することが必要となります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side) (based on the **Blazor (client-side)** project template, the `blazor` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) and is hosted as an IIS sub-app in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="6b50d-186">アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="6b50d-187">または、`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" ... />
      </handlers>
      <aspNetCore ... />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="6b50d-188">ハンドラーの削除または継承の無効化は、このセクションで説明したアプリのベース パスの構成に加えて実行されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="6b50d-189">IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="6b50d-190">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="6b50d-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="6b50d-191">"*ホストされた展開*" により、クライアント側の Blazor アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-191">A *hosted deployment* serves the Blazor client-side app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="6b50d-192">Blazor アプリは、発行された出力に ASP.NET Core アプリと共に含まれているため、2 つのアプリを一緒に展開することができます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="6b50d-193">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b50d-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="6b50d-194">ホストされている展開の場合、Visual Studio には **Blazor (ASP.NET Core でホストされる)** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorhosted` テンプレート)。</span><span class="sxs-lookup"><span data-stu-id="6b50d-194">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="6b50d-195">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6b50d-196">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="6b50d-197">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="6b50d-197">Standalone deployment</span></span>

<span data-ttu-id="6b50d-198">"*スタンドアロン展開*" により、クライアント側 Blazor アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-198">A *standalone deployment* serves the Blazor client-side app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="6b50d-199">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-199">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="6b50d-200">スタンドアロン展開の資産は *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-200">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="6b50d-201">IIS</span><span class="sxs-lookup"><span data-stu-id="6b50d-201">IIS</span></span>

<span data-ttu-id="6b50d-202">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-202">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="6b50d-203">Blazor をホストするよう IIS を構成する方法については、「[Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」 (IIS で静的 Web サイトを構築する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-203">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="6b50d-204">発行された資産は、 */bin/Release/<ターゲット フレームワーク>/publish* フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-204">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="6b50d-205">*publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-205">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="6b50d-206">web.config</span><span class="sxs-lookup"><span data-stu-id="6b50d-206">web.config</span></span>

<span data-ttu-id="6b50d-207">Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-207">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="6b50d-208">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-208">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="6b50d-209">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="6b50d-209">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="6b50d-210">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="6b50d-210">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="6b50d-211">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="6b50d-211">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="6b50d-212">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6b50d-212">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="6b50d-213">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6b50d-213">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="6b50d-214">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-214">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="6b50d-215">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-215">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="6b50d-216">アプリの静的なアセットが存在するサブディレクトリ ( *<アセンブリ名>/dist/<要求されたパス>* ) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-216">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="6b50d-217">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント ( *<アセンブリ名>/dist/index.html*) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-217">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="6b50d-218">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="6b50d-218">Install the URL Rewrite Module</span></span>

<span data-ttu-id="6b50d-219">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-219">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="6b50d-220">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-220">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="6b50d-221">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-221">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="6b50d-222">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-222">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="6b50d-223">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-223">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="6b50d-224">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-224">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="6b50d-225">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-225">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="6b50d-226">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-226">Copy the installer to the server.</span></span> <span data-ttu-id="6b50d-227">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-227">Run the installer.</span></span> <span data-ttu-id="6b50d-228">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-228">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="6b50d-229">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-229">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="6b50d-230">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="6b50d-230">Configure the website</span></span>

<span data-ttu-id="6b50d-231">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-231">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="6b50d-232">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-232">The folder contains:</span></span>

* <span data-ttu-id="6b50d-233">*web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-233">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="6b50d-234">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="6b50d-234">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="6b50d-235">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6b50d-235">Troubleshooting</span></span>

<span data-ttu-id="6b50d-236">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-236">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="6b50d-237">モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-237">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="6b50d-238">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-238">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="6b50d-239">IIS への展開に関するトラブルシューティングの詳細については、「<xref:host-and-deploy/iis/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-239">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="6b50d-240">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="6b50d-240">Azure Storage</span></span>

<span data-ttu-id="6b50d-241">Azure ストレージの静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="6b50d-241">Azure Storage static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="6b50d-242">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6b50d-242">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="6b50d-243">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="6b50d-243">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="6b50d-244">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-244">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="6b50d-245">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-245">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="6b50d-246">Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="6b50d-246">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="6b50d-247">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-247">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="6b50d-248">*index.html* BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="6b50d-248">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="6b50d-249">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-249">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="6b50d-250">Nginx</span><span class="sxs-lookup"><span data-stu-id="6b50d-250">Nginx</span></span>

<span data-ttu-id="6b50d-251">以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。</span><span class="sxs-lookup"><span data-stu-id="6b50d-251">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="6b50d-252">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-252">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="6b50d-253">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="6b50d-253">Nginx in Docker</span></span>

<span data-ttu-id="6b50d-254">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="6b50d-254">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="6b50d-255">*nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-255">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="6b50d-256">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-256">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="6b50d-257">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="6b50d-257">GitHub Pages</span></span>

<span data-ttu-id="6b50d-258">URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-258">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="6b50d-259">コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-259">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="6b50d-260">コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b50d-260">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="6b50d-261">組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-261">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="6b50d-262">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="6b50d-262">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
