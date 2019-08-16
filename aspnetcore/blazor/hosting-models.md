---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor クライアント側とサーバー側のホストモデルについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 64393e826cb17550085f468f5916fca55973908f
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993391"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="2ba4b-103">Blazor ホスティングモデルの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ba4b-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="2ba4b-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2ba4b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2ba4b-105">Blazor は、ブラウザーでブラウザーでクライアント側を実行するために設計された web フレームワークであり、ASP.NET Core (*Blazor サーバー側*) の [WebAssembly](https://webassembly.org/) ベースの .net ランタイム (*Blazor client 側*) またはサーバー側で作成されています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="2ba4b-106">ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="2ba4b-107">この記事で説明されているホスティングモデルのプロジェクトを作成<xref:blazor/get-started>するには、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="2ba4b-108">クライアント側</span><span class="sxs-lookup"><span data-stu-id="2ba4b-108">Client-side</span></span>

<span data-ttu-id="2ba4b-109">Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="2ba4b-110">Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="2ba4b-111">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="2ba4b-112">UI の更新とイベントの処理は、同じプロセス内で行われます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="2ba4b-113">アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor クライアント側:Blazor アプリは、ブラウザー内の UI スレッドで実行されます。](hosting-models/_static/client-side.png)

<span data-ttu-id="2ba4b-115">クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="2ba4b-116">**Blazor WebAssembly アプリ**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="2ba4b-117">ASP.NET Core アプリは、Blazor アプリをクライアントに提供します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="2ba4b-118">Blazor クライアント側アプリは、web API 呼び出しまたは[SignalR](xref:signalr/introduction)を使用して、ネットワーク経由でサーバーと通信できます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-118">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="2ba4b-119">テンプレートには、を処理する*blazor*スクリプトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="2ba4b-120">.NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="2ba4b-121">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="2ba4b-122">クライアント側ホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-122">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="2ba4b-123">.NET サーバー側の依存関係はありません。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="2ba4b-124">クライアントにダウンロードされた後、アプリは完全に機能しています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="2ba4b-125">クライアントのリソースと機能は完全に活用されています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="2ba4b-126">作業はサーバーからクライアントにオフロードされます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="2ba4b-127">アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="2ba4b-128">サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="2ba4b-129">クライアント側のホストには欠点があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-129">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="2ba4b-130">アプリは、ブラウザーの機能に制限されています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="2ba4b-131">サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="2ba4b-132">ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="2ba4b-133">.NET ランタイムとツールのサポートの成熟度は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="2ba4b-134">たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="2ba4b-135">サーバー側</span><span class="sxs-lookup"><span data-stu-id="2ba4b-135">Server-side</span></span>

<span data-ttu-id="2ba4b-136">サーバー側のホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="2ba4b-137">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ブラウザーは、SignalR 接続を介して、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。](hosting-models/_static/server-side.png)

<span data-ttu-id="2ba4b-139">サーバー側のホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor Server アプリ**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="2ba4b-140">ASP.NET Core アプリはサーバー側アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-140">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="2ba4b-141">ASP.NET Core アプリは、追加するアプリ`Startup`のクラスを参照します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="2ba4b-142">サーバー側サービス。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-142">Server-side services.</span></span>
* <span data-ttu-id="2ba4b-143">要求処理パイプラインに対するアプリ。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="2ba4b-144">*Blazor*スクリプト&dagger;は、クライアント接続を確立します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="2ba4b-145">アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="2ba4b-146">サーバー側ホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="2ba4b-147">ダウンロードサイズがクライアント側のアプリよりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-147">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="2ba4b-148">このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="2ba4b-149">サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="2ba4b-150">シンクライアントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-150">Thin clients are supported.</span></span> <span data-ttu-id="2ba4b-151">たとえば、サーバー側のアプリは、WebAssembly サポートされていないブラウザーや、リソースが制限されたデバイスで動作します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-151">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="2ba4b-152">アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="2ba4b-153">サーバー側のホストには欠点があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="2ba4b-154">通常、待機時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-154">Higher latency usually exists.</span></span> <span data-ttu-id="2ba4b-155">すべてのユーザーの操作には、ネットワークホップが関係します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="2ba4b-156">オフラインサポートはありません。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-156">There's no offline support.</span></span> <span data-ttu-id="2ba4b-157">クライアント接続が失敗した場合、アプリは動作を停止します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="2ba4b-158">多くのユーザーがいるアプリでは、スケーラビリティが困難です。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="2ba4b-159">サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="2ba4b-160">アプリを提供するには、ASP.NET Core サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="2ba4b-161">サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="2ba4b-162">&dagger;*Blazor*スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="2ba4b-163">同じサーバーへの再接続</span><span class="sxs-lookup"><span data-stu-id="2ba4b-163">Reconnection to the same server</span></span>

<span data-ttu-id="2ba4b-164">Blazor サーバー側アプリには、サーバーへのアクティブな SignalR 接続が必要です。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-164">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="2ba4b-165">接続が失われた場合、アプリはサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-165">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="2ba4b-166">クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-166">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="2ba4b-167">クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-167">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2ba4b-168">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-168">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="2ba4b-169">UI をカスタマイズするには、 *_Host*ページ`components-reconnect-modal` `id`でとしてを使用して要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-169">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="2ba4b-170">クライアントは、接続の状態に基づいて、次のいずれかの CSS クラスを使用して、この要素を更新します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-170">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="2ba4b-171">`components-reconnect-show`&ndash;接続が失われたことを示す UI を表示し、クライアントが再接続を試みていることを示します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-171">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="2ba4b-172">`components-reconnect-hide`&ndash;クライアントにアクティブな接続があり、UI が非表示になっています。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-172">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="2ba4b-173">`components-reconnect-failed`&ndash;再接続に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-173">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="2ba4b-174">再度再接続を試みるに`window.Blazor.reconnect()`は、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-174">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="2ba4b-175">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="2ba4b-175">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="2ba4b-176">Blazor サーバー側アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に設定するように既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-176">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2ba4b-177">これは、 *_Host*ページで設定します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-177">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="2ba4b-178">クライアントは、アプリの再レンダリングに使用されたのと同じ状態でサーバーに再接続します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-178">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="2ba4b-179">アプリの状態がまだメモリ内にある場合は、SignalR 接続が確立された後に、コンポーネントの状態が再び実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-179">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="2ba4b-180">Razor ページとビューからのステートフル対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="2ba4b-180">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="2ba4b-181">ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-181">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="2ba4b-182">ページまたはビューがレンダリングされると、コンポーネントは prerendered に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-182">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="2ba4b-183">その後、状態がまだメモリ内にある限り、クライアント接続が確立されると、アプリはコンポーネントの状態に再接続します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-183">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="2ba4b-184">たとえば、次の Razor ページでは、 `Counter`フォームを使用して指定された初期カウントを持つコンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-184">For example, the following Razor page renders a `Counter` component with an initial count that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="2ba4b-185">アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="2ba4b-185">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="2ba4b-186">Blazor サーバー側アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="2ba4b-186">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="2ba4b-187">場合によっては、Blazor サーバー側アプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-187">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="2ba4b-188">たとえば、接続の問題を診断するために、SignalR クライアントのログ記録を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-188">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="2ba4b-189">*Pages/_Host*ファイルで SignalR クライアントを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-189">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="2ba4b-190">*Blazor スクリプト*の`<script>`タグに属性を追加します。`autostart="false"`</span><span class="sxs-lookup"><span data-stu-id="2ba4b-190">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="2ba4b-191">を`Blazor.start`呼び出し、SignalR builder を指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="2ba4b-191">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="2ba4b-192">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2ba4b-192">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
