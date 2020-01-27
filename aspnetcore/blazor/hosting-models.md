---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor Webasと Blazor サーバーホスティングモデルについて理解します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
- blazor.webassembly.js
uid: blazor/hosting-models
ms.openlocfilehash: 2c66bede9c1e31b22fd1612fead556176d6f192b
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726867"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="49c67-103">Blazor ホスティングモデルの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49c67-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="49c67-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="49c67-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="49c67-105"> は、ブラウザーでブラウザーでクライアント側を実行するために設計された web フレームワークであり、 [webasに](https://webassembly.org/)基づく .net ランタイム ( *Blazor webassembly*、または ASP.NET Core ( *Blazor サーバー*) のサーバー側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="49c67-106">ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。</span><span class="sxs-lookup"><span data-stu-id="49c67-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="49c67-107">この記事で説明されているホスティングモデルのプロジェクトを作成するには、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="49c67-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="49c67-108"> WebAssembly</span></span>

<span data-ttu-id="49c67-109">Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="49c67-110">Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="49c67-111">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="49c67-112">UI の更新とイベントの処理は、同じプロセス内で行われます。</span><span class="sxs-lookup"><span data-stu-id="49c67-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="49c67-113">アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[!ファンド.NO LOC (Blazor)]<span data-ttu-id="49c67-114"> WebAssembly [!ファンド.NO LOC (Blazor)] アプリは、ブラウザー内の UI スレッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="49c67-115">クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="49c67-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="49c67-116">**Blazor WebAssembly**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([dotnet new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="49c67-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="49c67-117">ASP.NET Core アプリは、クライアントに対して Blazor アプリを提供します。</span><span class="sxs-lookup"><span data-stu-id="49c67-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="49c67-118">Blazor WebAssembly は、web API 呼び出しまたは[SignalR](xref:signalr/introduction)を使用して、ネットワーク経由でサーバーと通信できます。</span><span class="sxs-lookup"><span data-stu-id="49c67-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="49c67-119">テンプレートには、を処理する `blazor.webassembly.js` スクリプトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="49c67-119">The templates include the `blazor.webassembly.js` script that handles:</span></span>

* <span data-ttu-id="49c67-120">.NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。</span><span class="sxs-lookup"><span data-stu-id="49c67-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="49c67-121">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="49c67-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="49c67-122">Blazor WebAssembly ホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="49c67-123">.NET サーバー側の依存関係はありません。</span><span class="sxs-lookup"><span data-stu-id="49c67-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="49c67-124">クライアントにダウンロードされた後、アプリは完全に機能しています。</span><span class="sxs-lookup"><span data-stu-id="49c67-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="49c67-125">クライアントのリソースと機能は完全に活用されています。</span><span class="sxs-lookup"><span data-stu-id="49c67-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="49c67-126">作業はサーバーからクライアントにオフロードされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="49c67-127">アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="49c67-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="49c67-128">サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="49c67-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="49c67-129">Blazor Webasホストには欠点があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="49c67-130">アプリは、ブラウザーの機能に制限されています。</span><span class="sxs-lookup"><span data-stu-id="49c67-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="49c67-131">サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。</span><span class="sxs-lookup"><span data-stu-id="49c67-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="49c67-132">ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="49c67-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="49c67-133">.NET ランタイムとツールのサポートの成熟度は低くなります。</span><span class="sxs-lookup"><span data-stu-id="49c67-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="49c67-134">たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="49c67-135"> サーバー</span><span class="sxs-lookup"><span data-stu-id="49c67-135"> Server</span></span>

<span data-ttu-id="49c67-136">Blazor サーバーホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="49c67-137">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ブラウザーは、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。ファンド.NO LOC (SignalR)] 接続。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="49c67-139">Blazor Server ホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor サーバーアプリ**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="49c67-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="49c67-140">ASP.NET Core アプリは、Blazor Server アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="49c67-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="49c67-141">ASP.NET Core アプリは、追加するアプリの `Startup` クラスを参照します。</span><span class="sxs-lookup"><span data-stu-id="49c67-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="49c67-142">サーバー側サービス。</span><span class="sxs-lookup"><span data-stu-id="49c67-142">Server-side services.</span></span>
* <span data-ttu-id="49c67-143">要求処理パイプラインに対するアプリ。</span><span class="sxs-lookup"><span data-stu-id="49c67-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="49c67-144">`blazor.server.js` スクリプト&dagger; クライアント接続を確立します。</span><span class="sxs-lookup"><span data-stu-id="49c67-144">The `blazor.server.js` script&dagger; establishes the client connection.</span></span> <span data-ttu-id="49c67-145">アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。</span><span class="sxs-lookup"><span data-stu-id="49c67-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="49c67-146">Blazor サーバーホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="49c67-147">ダウンロードサイズは Blazor Webasapp よりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="49c67-148">このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="49c67-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="49c67-149">サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。</span><span class="sxs-lookup"><span data-stu-id="49c67-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="49c67-150">シンクライアントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="49c67-150">Thin clients are supported.</span></span> <span data-ttu-id="49c67-151">たとえば、Blazor サーバーアプリは、WebAssembly サポートされていないブラウザーや、リソースが制限されたデバイスで動作します。</span><span class="sxs-lookup"><span data-stu-id="49c67-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="49c67-152">アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。</span><span class="sxs-lookup"><span data-stu-id="49c67-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="49c67-153">Blazor サーバーのホストには欠点があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="49c67-154">通常、待機時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="49c67-154">Higher latency usually exists.</span></span> <span data-ttu-id="49c67-155">すべてのユーザーの操作には、ネットワークホップが関係します。</span><span class="sxs-lookup"><span data-stu-id="49c67-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="49c67-156">オフラインサポートはありません。</span><span class="sxs-lookup"><span data-stu-id="49c67-156">There's no offline support.</span></span> <span data-ttu-id="49c67-157">クライアント接続が失敗した場合、アプリは動作を停止します。</span><span class="sxs-lookup"><span data-stu-id="49c67-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="49c67-158">多くのユーザーがいるアプリでは、スケーラビリティが困難です。</span><span class="sxs-lookup"><span data-stu-id="49c67-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="49c67-159">サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="49c67-160">アプリを提供するには、ASP.NET Core サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="49c67-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="49c67-161">サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="49c67-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="49c67-162">&dagger;`blazor.server.js` スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-162">&dagger;The `blazor.server.js` script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="49c67-163">サーバーレンダリングの UI との比較</span><span class="sxs-lookup"><span data-stu-id="49c67-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="49c67-164">Blazor サーバーアプリを理解する方法の1つは、Razor ビューまたは Razor Pages を使用する ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。</span><span class="sxs-lookup"><span data-stu-id="49c67-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="49c67-165">どちらのモデルでも、Razor 言語を使用して HTML コンテンツを記述しますが、マークアップのレンダリング方法が大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="49c67-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="49c67-166">Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="49c67-167">レンダリング後、サーバーは、生成されたすべての状態を含むページまたはビューインスタンスを破棄します。</span><span class="sxs-lookup"><span data-stu-id="49c67-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="49c67-168">サーバーの検証に失敗し、検証の概要が表示される場合など、ページに対する別の要求が発生したとき。</span><span class="sxs-lookup"><span data-stu-id="49c67-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="49c67-169">ページ全体が HTML テキストに再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="49c67-170">ページがクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-170">The page is sent to the client.</span></span>

<span data-ttu-id="49c67-171">Blazor アプリは、*コンポーネント*と呼ばれる UI の再利用可能な要素で構成されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="49c67-172">コンポーネントにはC# 、コード、マークアップ、およびその他のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="49c67-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="49c67-173">コンポーネントがレンダリングされると、Blazor は HTML または XML ドキュメントオブジェクトモデル (DOM) のような、含まれているコンポーネントのグラフを生成します。</span><span class="sxs-lookup"><span data-stu-id="49c67-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="49c67-174">このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="49c67-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="49c67-175"> は、コンポーネントグラフを評価して、マークアップのバイナリ表現を生成します。</span><span class="sxs-lookup"><span data-stu-id="49c67-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="49c67-176">バイナリ形式は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="49c67-176">The binary format can be:</span></span>

* <span data-ttu-id="49c67-177">(プリレンダリング中に) HTML テキストに変換されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="49c67-178">通常のレンダリング中にマークアップを効率的に更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="49c67-179">Blazor の UI の更新は、次の方法でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="49c67-180">ユーザー操作 (ボタンの選択など)。</span><span class="sxs-lookup"><span data-stu-id="49c67-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="49c67-181">タイマーなどのアプリトリガー。</span><span class="sxs-lookup"><span data-stu-id="49c67-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="49c67-182">グラフが再ピアリングされ、UI *diff* (差分) が計算されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="49c67-183">この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。</span><span class="sxs-lookup"><span data-stu-id="49c67-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="49c67-184">Diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="49c67-185">コンポーネントは、ユーザーがクライアント上で移動した後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="49c67-186">ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) は、サーバーのメモリに保持されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="49c67-187">多くのコンポーネントの状態は同時にサーバーによって維持される可能性があるため、メモリ不足に対処する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="49c67-188">サーバーメモリを最大限に活用するために Blazor Server アプリを作成する方法については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="49c67-189">Razor コンポーネントを Razor Pages および MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="49c67-189">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="49c67-190">ページおよびビューでのコンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="49c67-190">Use components in pages and views</span></span>

<span data-ttu-id="49c67-191">既存の Razor Pages または MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="49c67-191">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="49c67-192">アプリのレイアウトファイル ( *_Layout*) で、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="49c67-192">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="49c67-193">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-193">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="49c67-194">前の例の `href` 値 (*アプリのベースパス*) は、アプリがルート URL パス (`/`) に存在することを前提としています。</span><span class="sxs-lookup"><span data-stu-id="49c67-194">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="49c67-195">アプリがサブアプリケーションである場合は、<xref:host-and-deploy/blazor/index#app-base-path> に関する記事の「*アプリの基本パス*」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="49c67-195">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="49c67-196">*_Layout*のファイルは、MVC アプリの Razor Pages アプリまたは*Views/shared*フォルダー内の*Pages/shared*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="49c67-196">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="49c67-197">Blazor スクリプトの `<script>` タグを、終了 `</body>` タグの内側に追加*し*ます。</span><span class="sxs-lookup"><span data-stu-id="49c67-197">Add a `<script>` tag for the *blazor.server.js* script inside of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="49c67-198">フレームワークによって、 *blazor*スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-198">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="49c67-199">アプリにスクリプトを手動で追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="49c67-199">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="49c67-200">次の内容を使用して、プロジェクトのルートフォルダーに *_Imports razor*ファイルを追加します (最後の名前空間、`MyAppNamespace`をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="49c67-200">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="49c67-201">`Startup.ConfigureServices`で、Blazor Server サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-201">In `Startup.ConfigureServices`, add the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="49c67-202">`Startup.Configure`で、`app.UseEndpoints`に Blazor ハブエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-202">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="49c67-203">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="49c67-203">Integrate components into any page or view.</span></span> <span data-ttu-id="49c67-204">詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> の記事の「*コンポーネントを Razor Pages と MVC アプリに統合*する」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-204">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="49c67-205">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="49c67-205">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="49c67-206">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには:</span><span class="sxs-lookup"><span data-stu-id="49c67-206">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="49c67-207">「[ページおよびビューでコンポーネントを使用する](#use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="49c67-207">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="49c67-208">次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-208">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="49c67-209">次の内容を含む *_Host*ファイルを*Pages*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-209">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="49c67-210">コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="49c67-210">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="49c67-211">`Startup.Configure`のエンドポイント構成に *_Host*の、次のように低優先度のルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-211">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="49c67-212">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-212">Add routable components to the app.</span></span> <span data-ttu-id="49c67-213">例:</span><span class="sxs-lookup"><span data-stu-id="49c67-213">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="49c67-214">カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を*Pages/_ViewImports cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-214">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="49c67-215">詳細については、「 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-215">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="49c67-216">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="49c67-216">Use routable components in an MVC app</span></span>

<span data-ttu-id="49c67-217">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="49c67-217">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="49c67-218">「[ページおよびビューでコンポーネントを使用する](#use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="49c67-218">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="49c67-219">次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-219">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="49c67-220">次の内容を含む *_Host*ファイルを*Views/Home*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-220">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="49c67-221">コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="49c67-221">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="49c67-222">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-222">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="49c67-223">`Startup.Configure`のエンドポイント構成に *_Host*のビューを返すコントローラーアクションの優先度の低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-223">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="49c67-224">*ページ*フォルダーを作成し、ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-224">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="49c67-225">例:</span><span class="sxs-lookup"><span data-stu-id="49c67-225">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="49c67-226">カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を*Views/_ViewImports cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-226">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="49c67-227">詳細については、「 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-227">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="circuits"></a><span data-ttu-id="49c67-228">接続</span><span class="sxs-lookup"><span data-stu-id="49c67-228">Circuits</span></span>

<span data-ttu-id="49c67-229">Blazor サーバーアプリは[ASP.NET Core SignalR](xref:signalr/introduction)の上に構築されています。</span><span class="sxs-lookup"><span data-stu-id="49c67-229">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="49c67-230">各クライアントは、*回線*と呼ばれる1つ以上の SignalR 接続を介してサーバーと通信します。</span><span class="sxs-lookup"><span data-stu-id="49c67-230">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="49c67-231">回線は、一時的なネットワーク中断が許容される SignalR 接続に対して Blazor抽象化されています。</span><span class="sxs-lookup"><span data-stu-id="49c67-231">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="49c67-232">Blazor クライアントは、SignalR 接続が切断されていることを確認すると、新しい SignalR 接続を使用してサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="49c67-232">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="49c67-233">Blazor Server アプリに接続されている各ブラウザー画面 (ブラウザータブまたは iframe) は、SignalR 接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="49c67-233">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="49c67-234">これは、サーバーでレンダリングされる一般的なアプリと比較して、もう1つ重要な違いです。</span><span class="sxs-lookup"><span data-stu-id="49c67-234">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="49c67-235">サーバー側でレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開くのは、通常、サーバーに対する追加のリソース要求には変換されません。</span><span class="sxs-lookup"><span data-stu-id="49c67-235">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="49c67-236">Blazor サーバーアプリでは、各ブラウザー画面に個別の回線が必要で、コンポーネント状態の個別のインスタンスがサーバーによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-236">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="49c67-237"> は、ブラウザータブを閉じるか、外部 URL に移動して*正常*に終了すると見なされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-237"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="49c67-238">正常な終了が発生した場合、回線と関連するリソースが直ちに解放されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-238">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="49c67-239">クライアントは、ネットワークの中断などによって、正常に切断されることもあります。</span><span class="sxs-lookup"><span data-stu-id="49c67-239">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="49c67-240"> サーバーは、クライアントが再接続できるように、接続されていない回線を構成可能な間隔で格納します。</span><span class="sxs-lookup"><span data-stu-id="49c67-240"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="49c67-241">詳細については、「[同じサーバーへ](#reconnection-to-the-same-server)の再接続」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-241">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="49c67-242">UI の待機時間</span><span class="sxs-lookup"><span data-stu-id="49c67-242">UI Latency</span></span>

<span data-ttu-id="49c67-243">UI 待機時間とは、開始されたアクションから UI が更新されるまでにかかる時間のことです。</span><span class="sxs-lookup"><span data-stu-id="49c67-243">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="49c67-244">アプリがユーザーに応答できるようにするには、UI の待機時間の値を小さくすることが不可欠です。</span><span class="sxs-lookup"><span data-stu-id="49c67-244">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="49c67-245">Blazor Server アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-245">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="49c67-246">その結果、UI の待機時間は、ネットワーク待機時間の合計と、アクションを処理するサーバーの待機時間です。</span><span class="sxs-lookup"><span data-stu-id="49c67-246">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="49c67-247">企業のプライベートネットワークに限定された基幹業務アプリの場合、ネットワーク待機時間による待ち時間のユーザーへの影響は、通常はなるべくです。</span><span class="sxs-lookup"><span data-stu-id="49c67-247">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="49c67-248">インターネット経由で展開されたアプリの場合、ユーザーにとって待機時間が顕著になる可能性があります。ユーザーが地理的に広く分散している場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="49c67-248">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="49c67-249">メモリ使用量は、アプリの待機時間に寄与する場合もあります。</span><span class="sxs-lookup"><span data-stu-id="49c67-249">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="49c67-250">メモリ使用量が増加すると、ガベージコレクションまたはメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI の遅延が増加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-250">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="49c67-251">詳細については、「 <xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-251">For more information, see <xref:security/blazor/server>.</span></span>

Blazor<span data-ttu-id="49c67-252"> サーバーアプリは、ネットワーク待機時間とメモリ使用量を削減することで、UI の待機時間を最小限に抑えるように最適化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-252"> Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="49c67-253">ネットワーク待機時間を測定する方法については、「<xref:host-and-deploy/blazor/server#measure-network-latency>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-253">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="49c67-254">SignalR と Blazorの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-254">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="49c67-255">サーバーへの接続</span><span class="sxs-lookup"><span data-stu-id="49c67-255">Connection to the server</span></span>

Blazor<span data-ttu-id="49c67-256"> サーバーアプリには、サーバーへのアクティブな SignalR 接続が必要です。</span><span class="sxs-lookup"><span data-stu-id="49c67-256"> Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="49c67-257">接続が失われた場合、アプリはサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="49c67-257">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="49c67-258">クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-258">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="49c67-259">同じサーバーへの再接続</span><span class="sxs-lookup"><span data-stu-id="49c67-259">Reconnection to the same server</span></span>

<span data-ttu-id="49c67-260">最初のクライアント要求への応答としての Blazor Server アプリ prerenders。これにより、サーバー上で UI の状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-260">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="49c67-261">クライアントが SignalR 接続を作成しようとすると、クライアントは同じサーバーに再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-261">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="49c67-262">複数のバックエンドサーバーを使用する Blazor サーバーアプリは、SignalR 接続用の*固定セッション*を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-262">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="49c67-263">Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="49c67-263">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="49c67-264">このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="49c67-264">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="49c67-265">Azure SignalR サービスでは、サービスの `ServerStickyMode` オプションまたは構成値を `Required`に設定することにより、固定セッションが有効になります。</span><span class="sxs-lookup"><span data-stu-id="49c67-265">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="49c67-266">詳細については、「 <xref:host-and-deploy/blazor/server#signalr-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49c67-266">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="49c67-267">IIS を使用すると、スティッキー セッションはアプリケーション要求ルーティングによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="49c67-267">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="49c67-268">詳しくは、「[アプリケーション要求ルーティングを使用した HTTP 負荷分散](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="49c67-268">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="49c67-269">UI の接続状態を反映します。</span><span class="sxs-lookup"><span data-stu-id="49c67-269">Reflect the connection state in the UI</span></span>

<span data-ttu-id="49c67-270">クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-270">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="49c67-271">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-271">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="49c67-272">UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="49c67-272">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="49c67-273">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="49c67-273">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="49c67-274">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="49c67-274">CSS class</span></span>                       | <span data-ttu-id="49c67-275">&hellip; を示します。</span><span class="sxs-lookup"><span data-stu-id="49c67-275">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="49c67-276">失われた接続。</span><span class="sxs-lookup"><span data-stu-id="49c67-276">A lost connection.</span></span> <span data-ttu-id="49c67-277">クライアントが再接続しようとしています。</span><span class="sxs-lookup"><span data-stu-id="49c67-277">The client is attempting to reconnect.</span></span> <span data-ttu-id="49c67-278">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="49c67-278">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="49c67-279">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-279">An active connection is re-established to the server.</span></span> <span data-ttu-id="49c67-280">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="49c67-280">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="49c67-281">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-281">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="49c67-282">再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="49c67-282">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="49c67-283">再接続は拒否されました。</span><span class="sxs-lookup"><span data-stu-id="49c67-283">Reconnection rejected.</span></span> <span data-ttu-id="49c67-284">サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。</span><span class="sxs-lookup"><span data-stu-id="49c67-284">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="49c67-285">アプリを再度読み込むには、`location.reload()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="49c67-285">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="49c67-286">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-286">This connection state may result when:</span></span><ul><li><span data-ttu-id="49c67-287">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="49c67-287">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="49c67-288">サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。</span><span class="sxs-lookup"><span data-stu-id="49c67-288">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="49c67-289">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-289">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="49c67-290">サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</span><span class="sxs-lookup"><span data-stu-id="49c67-290">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="49c67-291">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="49c67-291">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="49c67-292">サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために、Blazor サーバーアプリが既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-292">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="49c67-293">これは *_Host. cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-293">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="49c67-294">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="49c67-294">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="49c67-295">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="49c67-295">Is prerendered into the page.</span></span>
* <span data-ttu-id="49c67-296">は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-296">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="49c67-297">説明</span><span class="sxs-lookup"><span data-stu-id="49c67-297">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="49c67-298">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="49c67-298">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="49c67-299">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-299">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="49c67-300">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="49c67-300">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="49c67-301">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="49c67-301">Output from the component isn't included.</span></span> <span data-ttu-id="49c67-302">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-302">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="49c67-303">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="49c67-303">Renders the component into static HTML.</span></span> |

<span data-ttu-id="49c67-304">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="49c67-304">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="49c67-305">`RenderMode` が `ServerPrerendered`場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-305">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="49c67-306">ブラウザーがサーバーへの接続を確立すると、コンポーネントが*再び*表示され、コンポーネントが対話型になります。</span><span class="sxs-lookup"><span data-stu-id="49c67-306">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="49c67-307">コンポーネントを初期化するための[Oninitialized 化された {Async}](xref:blazor/lifecycle#component-initialization-methods)ライフサイクルメソッドが存在する場合、メソッドは*2 回*実行されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-307">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="49c67-308">コンポーネントが静的に prerendered された場合。</span><span class="sxs-lookup"><span data-stu-id="49c67-308">When the component is prerendered statically.</span></span>
* <span data-ttu-id="49c67-309">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="49c67-309">After the server connection has been established.</span></span>

<span data-ttu-id="49c67-310">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変化する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-310">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="49c67-311">Blazor サーバーアプリでダブルレンダリングのシナリオを回避するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="49c67-311">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="49c67-312">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="49c67-312">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="49c67-313">コンポーネントの状態を保存するには、プリレンダリング時に識別子を使用します。</span><span class="sxs-lookup"><span data-stu-id="49c67-313">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="49c67-314">プリレンダリング後に識別子を使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="49c67-314">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="49c67-315">次のコードは、テンプレートベースの Blazor サーバーアプリで、ダブルレンダリングを回避する更新された `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="49c67-315">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="49c67-316">Razor ページとビューからのステートフル対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="49c67-316">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="49c67-317">ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。</span><span class="sxs-lookup"><span data-stu-id="49c67-317">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="49c67-318">ページまたはビューが表示される場合:</span><span class="sxs-lookup"><span data-stu-id="49c67-318">When the page or view renders:</span></span>

* <span data-ttu-id="49c67-319">コンポーネントは、ページまたはビューと prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="49c67-319">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="49c67-320">プリレンダリングに使用される初期コンポーネントの状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="49c67-320">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="49c67-321">SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="49c67-321">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="49c67-322">次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-322">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="49c67-323">Razor ページとビューからの非対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="49c67-323">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="49c67-324">次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="49c67-324">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="49c67-325">`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="49c67-325">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="49c67-326">アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="49c67-326">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="49c67-327">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="49c67-327">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="49c67-328">場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49c67-328">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="49c67-329">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="49c67-329">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="49c67-330">*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="49c67-330">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="49c67-331">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="49c67-331">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="49c67-332">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="49c67-332">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="49c67-333">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="49c67-333">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
