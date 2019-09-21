---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor WebAssembly と Blazor のサーバーホスティングモデルを理解します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/hosting-models
ms.openlocfilehash: 47c546a086588919e4458d6aeeb39453cbc754e0
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168143"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="04357-103">Blazor ホスティングモデルの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04357-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="04357-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="04357-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="04357-105">Blazor は、ブラウザーでブラウザーでクライアント側を実行するように設計された web フレームワークで、 [WEBAS.NET](https://webassembly.org/)ランタイム (*Blazor Webassembly*) またはサーバー ASP.NET Core 側 (*Blazor サーバー*) で実行します。</span><span class="sxs-lookup"><span data-stu-id="04357-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="04357-106">ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。</span><span class="sxs-lookup"><span data-stu-id="04357-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="04357-107">この記事で説明されているホスティングモデルのプロジェクトを作成<xref:blazor/get-started>するには、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="04357-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="04357-108">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="04357-108">Blazor WebAssembly</span></span>

<span data-ttu-id="04357-109">Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="04357-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="04357-110">Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="04357-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="04357-111">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="04357-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="04357-112">UI の更新とイベントの処理は、同じプロセス内で行われます。</span><span class="sxs-lookup"><span data-stu-id="04357-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="04357-113">アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。</span><span class="sxs-lookup"><span data-stu-id="04357-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor Webas:Blazor アプリは、ブラウザー内の UI スレッドで実行されます。](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="04357-115">クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="04357-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="04357-116">**Blazor WebAssembly アプリ**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="04357-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="04357-117">ASP.NET Core アプリは、Blazor アプリをクライアントに提供します。</span><span class="sxs-lookup"><span data-stu-id="04357-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="04357-118">Blazor WebAssembly は、web API 呼び出しまたは[SignalR](xref:signalr/introduction)を使用して、ネットワーク経由でサーバーと通信できます。</span><span class="sxs-lookup"><span data-stu-id="04357-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="04357-119">テンプレートには、を処理する*blazor*スクリプトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="04357-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="04357-120">.NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。</span><span class="sxs-lookup"><span data-stu-id="04357-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="04357-121">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="04357-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="04357-122">Blazor WebAssembly ホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="04357-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="04357-123">.NET サーバー側の依存関係はありません。</span><span class="sxs-lookup"><span data-stu-id="04357-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="04357-124">クライアントにダウンロードされた後、アプリは完全に機能しています。</span><span class="sxs-lookup"><span data-stu-id="04357-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="04357-125">クライアントのリソースと機能は完全に活用されています。</span><span class="sxs-lookup"><span data-stu-id="04357-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="04357-126">作業はサーバーからクライアントにオフロードされます。</span><span class="sxs-lookup"><span data-stu-id="04357-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="04357-127">アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="04357-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="04357-128">サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="04357-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="04357-129">Blazor WebAssembly には欠点があります。</span><span class="sxs-lookup"><span data-stu-id="04357-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="04357-130">アプリは、ブラウザーの機能に制限されています。</span><span class="sxs-lookup"><span data-stu-id="04357-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="04357-131">サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。</span><span class="sxs-lookup"><span data-stu-id="04357-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="04357-132">ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="04357-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="04357-133">.NET ランタイムとツールのサポートの成熟度は低くなります。</span><span class="sxs-lookup"><span data-stu-id="04357-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="04357-134">たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。</span><span class="sxs-lookup"><span data-stu-id="04357-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="04357-135">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="04357-135">Blazor Server</span></span>

<span data-ttu-id="04357-136">Blazor Server ホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="04357-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="04357-137">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="04357-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ブラウザーは、SignalR 接続を介して、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="04357-139">Blazor サーバーホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor Server アプリケーション**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="04357-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="04357-140">ASP.NET Core アプリは Blazor Server アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="04357-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="04357-141">ASP.NET Core アプリは、追加するアプリ`Startup`のクラスを参照します。</span><span class="sxs-lookup"><span data-stu-id="04357-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="04357-142">サーバー側サービス。</span><span class="sxs-lookup"><span data-stu-id="04357-142">Server-side services.</span></span>
* <span data-ttu-id="04357-143">要求処理パイプラインに対するアプリ。</span><span class="sxs-lookup"><span data-stu-id="04357-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="04357-144">*Blazor*スクリプト&dagger;は、クライアント接続を確立します。</span><span class="sxs-lookup"><span data-stu-id="04357-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="04357-145">アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。</span><span class="sxs-lookup"><span data-stu-id="04357-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="04357-146">Blazor サーバーホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="04357-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="04357-147">ダウンロードサイズは、Blazor Webasアプリよりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="04357-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="04357-148">このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="04357-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="04357-149">サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。</span><span class="sxs-lookup"><span data-stu-id="04357-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="04357-150">シンクライアントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="04357-150">Thin clients are supported.</span></span> <span data-ttu-id="04357-151">たとえば、Blazor Server apps は、WebAssembly サポートされていないブラウザーや、リソースに制約のあるデバイスで動作します。</span><span class="sxs-lookup"><span data-stu-id="04357-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="04357-152">アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。</span><span class="sxs-lookup"><span data-stu-id="04357-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="04357-153">Blazor サーバーホストには、次のような欠点があります。</span><span class="sxs-lookup"><span data-stu-id="04357-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="04357-154">通常、待機時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="04357-154">Higher latency usually exists.</span></span> <span data-ttu-id="04357-155">すべてのユーザーの操作には、ネットワークホップが関係します。</span><span class="sxs-lookup"><span data-stu-id="04357-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="04357-156">オフラインサポートはありません。</span><span class="sxs-lookup"><span data-stu-id="04357-156">There's no offline support.</span></span> <span data-ttu-id="04357-157">クライアント接続が失敗した場合、アプリは動作を停止します。</span><span class="sxs-lookup"><span data-stu-id="04357-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="04357-158">多くのユーザーがいるアプリでは、スケーラビリティが困難です。</span><span class="sxs-lookup"><span data-stu-id="04357-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="04357-159">サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="04357-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="04357-160">アプリを提供するには、ASP.NET Core サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="04357-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="04357-161">サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="04357-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="04357-162">&dagger;*Blazor*スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。</span><span class="sxs-lookup"><span data-stu-id="04357-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="04357-163">サーバーレンダリングの UI との比較</span><span class="sxs-lookup"><span data-stu-id="04357-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="04357-164">Blazor Server アプリを理解する方法の1つは、Razor ビューまたは Razor Pages を使用して ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。</span><span class="sxs-lookup"><span data-stu-id="04357-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="04357-165">どちらのモデルでも、Razor 言語を使用して HTML コンテンツを記述しますが、マークアップのレンダリング方法が大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="04357-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="04357-166">Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。</span><span class="sxs-lookup"><span data-stu-id="04357-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="04357-167">レンダリング後、サーバーは、生成されたすべての状態を含むページまたはビューインスタンスを破棄します。</span><span class="sxs-lookup"><span data-stu-id="04357-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="04357-168">サーバーの検証に失敗し、検証の概要が表示される場合など、ページに対する別の要求が発生したとき。</span><span class="sxs-lookup"><span data-stu-id="04357-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="04357-169">ページ全体が HTML テキストに再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="04357-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="04357-170">ページがクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="04357-170">The page is sent to the client.</span></span>

<span data-ttu-id="04357-171">Blazor アプリは、*コンポーネント*と呼ばれる UI の再利用可能な要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="04357-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="04357-172">コンポーネントにはC# 、コード、マークアップ、およびその他のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="04357-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="04357-173">コンポーネントがレンダリングされると、Blazor は HTML または XML ドキュメントオブジェクトモデル (DOM) のような、含まれているコンポーネントのグラフを生成します。</span><span class="sxs-lookup"><span data-stu-id="04357-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="04357-174">このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="04357-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="04357-175">Blazor は、コンポーネントグラフを評価して、マークアップのバイナリ表現を生成します。</span><span class="sxs-lookup"><span data-stu-id="04357-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="04357-176">バイナリ形式は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="04357-176">The binary format can be:</span></span>

* <span data-ttu-id="04357-177">(プリレンダリング中に) HTML テキストに変換されます。</span><span class="sxs-lookup"><span data-stu-id="04357-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="04357-178">通常のレンダリング中にマークアップを効率的に更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="04357-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="04357-179">Blazor の UI 更新は、次の方法でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="04357-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="04357-180">ユーザー操作 (ボタンの選択など)。</span><span class="sxs-lookup"><span data-stu-id="04357-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="04357-181">タイマーなどのアプリトリガー。</span><span class="sxs-lookup"><span data-stu-id="04357-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="04357-182">グラフが再ピアリングされ、UI *diff* (差分) が計算されます。</span><span class="sxs-lookup"><span data-stu-id="04357-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="04357-183">この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。</span><span class="sxs-lookup"><span data-stu-id="04357-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="04357-184">Diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="04357-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="04357-185">コンポーネントは、ユーザーがクライアント上で移動した後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="04357-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="04357-186">ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) は、サーバーのメモリに保持されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="04357-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="04357-187">多くのコンポーネントの状態は同時にサーバーによって維持される可能性があるため、メモリ不足に対処する必要があります。</span><span class="sxs-lookup"><span data-stu-id="04357-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="04357-188">Blazor Server アプリを作成してサーバーのメモリを最大限に活用する方法については<xref:security/blazor/server>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="04357-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="04357-189">接続</span><span class="sxs-lookup"><span data-stu-id="04357-189">Circuits</span></span>

<span data-ttu-id="04357-190">Blazor Server アプリは、 [ASP.NET Core SignalR](xref:signalr/introduction)の上に構築されています。</span><span class="sxs-lookup"><span data-stu-id="04357-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="04357-191">各クライアントは、*回線*と呼ばれる1つ以上の SignalR 接続を介してサーバーと通信します。</span><span class="sxs-lookup"><span data-stu-id="04357-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="04357-192">回線は、一時的なネットワーク中断を許容できる SignalR 接続に対する Blazor の抽象化です。</span><span class="sxs-lookup"><span data-stu-id="04357-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="04357-193">Blazor クライアントは、SignalR 接続が切断されていることを確認すると、新しい SignalR 接続を使用してサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="04357-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="04357-194">Blazor Server アプリに接続されている各ブラウザー画面 (ブラウザータブまたは iframe) は、SignalR 接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="04357-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="04357-195">これは、サーバーでレンダリングされる一般的なアプリと比較して、もう1つ重要な違いです。</span><span class="sxs-lookup"><span data-stu-id="04357-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="04357-196">サーバー側でレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開くのは、通常、サーバーに対する追加のリソース要求には変換されません。</span><span class="sxs-lookup"><span data-stu-id="04357-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="04357-197">Blazor Server アプリでは、各ブラウザー画面に個別の回線が必要で、コンポーネント状態の個別のインスタンスがサーバーによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="04357-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="04357-198">Blazor は、ブラウザータブを閉じるか、外部 URL に移動して*正常*に終了することを検討します。</span><span class="sxs-lookup"><span data-stu-id="04357-198">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="04357-199">正常な終了が発生した場合、回線と関連するリソースが直ちに解放されます。</span><span class="sxs-lookup"><span data-stu-id="04357-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="04357-200">クライアントは、ネットワークの中断などによって、正常に切断されることもあります。</span><span class="sxs-lookup"><span data-stu-id="04357-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="04357-201">Blazor Server は、クライアントが再接続できるように、接続されていない回線を構成可能な間隔で格納します。</span><span class="sxs-lookup"><span data-stu-id="04357-201">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="04357-202">詳細については、「[同じサーバーへ](#reconnection-to-the-same-server)の再接続」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="04357-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="04357-203">UI の待機時間</span><span class="sxs-lookup"><span data-stu-id="04357-203">UI Latency</span></span>

<span data-ttu-id="04357-204">UI 待機時間とは、開始されたアクションから UI が更新されるまでにかかる時間のことです。</span><span class="sxs-lookup"><span data-stu-id="04357-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="04357-205">アプリがユーザーに応答できるようにするには、UI の待機時間の値を小さくすることが不可欠です。</span><span class="sxs-lookup"><span data-stu-id="04357-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="04357-206">Blazor Server アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。</span><span class="sxs-lookup"><span data-stu-id="04357-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="04357-207">その結果、UI の待機時間は、ネットワーク待機時間の合計と、アクションを処理するサーバーの待機時間です。</span><span class="sxs-lookup"><span data-stu-id="04357-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="04357-208">企業のプライベートネットワークに限定された基幹業務アプリの場合、ネットワーク待機時間による待ち時間のユーザーへの影響は、通常はなるべくです。</span><span class="sxs-lookup"><span data-stu-id="04357-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="04357-209">インターネット経由で展開されたアプリの場合、ユーザーにとって待機時間が顕著になる可能性があります。ユーザーが地理的に広く分散している場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="04357-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="04357-210">メモリ使用量は、アプリの待機時間に寄与する場合もあります。</span><span class="sxs-lookup"><span data-stu-id="04357-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="04357-211">メモリ使用量が増加すると、ガベージコレクションまたはメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI の遅延が増加します。</span><span class="sxs-lookup"><span data-stu-id="04357-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="04357-212">詳細については、「 <xref:security/blazor/server> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="04357-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="04357-213">Blazor サーバーアプリは、ネットワーク待機時間とメモリ使用量を削減することで、UI の待機時間を最小限に抑えるように最適化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="04357-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="04357-214">ネットワーク待機時間を測定する方法につい<xref:host-and-deploy/blazor/server#measure-network-latency>ては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="04357-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="04357-215">SignalR と Blazor の詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="04357-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="04357-216">同じサーバーへの再接続</span><span class="sxs-lookup"><span data-stu-id="04357-216">Reconnection to the same server</span></span>

<span data-ttu-id="04357-217">Blazor サーバーアプリには、サーバーへのアクティブな SignalR 接続が必要です。</span><span class="sxs-lookup"><span data-stu-id="04357-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="04357-218">接続が失われた場合、アプリはサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="04357-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="04357-219">クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。</span><span class="sxs-lookup"><span data-stu-id="04357-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="04357-220">クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="04357-220">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="04357-221">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="04357-221">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="04357-222">UI をカスタマイズするには、 *_Host*ページ`components-reconnect-modal` `id`でとしてを使用して要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="04357-222">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="04357-223">クライアントは、接続の状態に基づいて、次のいずれかの CSS クラスを使用して、この要素を更新します。</span><span class="sxs-lookup"><span data-stu-id="04357-223">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>

* <span data-ttu-id="04357-224">`components-reconnect-show`&ndash;接続が失われたことを示す UI を表示し、クライアントが再接続を試みていることを示します。</span><span class="sxs-lookup"><span data-stu-id="04357-224">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="04357-225">`components-reconnect-hide`&ndash;クライアントにアクティブな接続があり、UI が非表示になっています。</span><span class="sxs-lookup"><span data-stu-id="04357-225">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="04357-226">`components-reconnect-failed`&ndash;再接続に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="04357-226">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="04357-227">再度再接続を試みるに`window.Blazor.reconnect()`は、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="04357-227">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="04357-228">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="04357-228">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="04357-229">Blazor サーバーアプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="04357-229">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="04357-230">これは、 *_Host*ページで設定します。</span><span class="sxs-lookup"><span data-stu-id="04357-230">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="04357-231">`RenderMode`コンポーネントを構成するかどうかを構成します。</span><span class="sxs-lookup"><span data-stu-id="04357-231">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="04357-232">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="04357-232">Is prerendered into the page.</span></span>
* <span data-ttu-id="04357-233">は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="04357-233">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="04357-234">説明</span><span class="sxs-lookup"><span data-stu-id="04357-234">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="04357-235">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="04357-235">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="04357-236">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="04357-236">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="04357-237">パラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="04357-237">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="04357-238">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="04357-238">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="04357-239">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="04357-239">Output from the component isn't included.</span></span> <span data-ttu-id="04357-240">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="04357-240">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="04357-241">パラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="04357-241">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="04357-242">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="04357-242">Renders the component into static HTML.</span></span> <span data-ttu-id="04357-243">パラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="04357-243">Parameters are supported.</span></span> |

<span data-ttu-id="04357-244">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="04357-244">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="04357-245">クライアントは、アプリの再レンダリングに使用されたのと同じ状態でサーバーに再接続します。</span><span class="sxs-lookup"><span data-stu-id="04357-245">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="04357-246">アプリの状態がまだメモリ内にある場合は、SignalR 接続が確立された後に、コンポーネントの状態が再び実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="04357-246">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="04357-247">Razor ページとビューからのステートフル対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="04357-247">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="04357-248">ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。</span><span class="sxs-lookup"><span data-stu-id="04357-248">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="04357-249">ページまたはビューが表示される場合:</span><span class="sxs-lookup"><span data-stu-id="04357-249">When the page or view renders:</span></span>

* <span data-ttu-id="04357-250">コンポーネントは、ページまたはビューと prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="04357-250">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="04357-251">プリレンダリングに使用される初期コンポーネントの状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="04357-251">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="04357-252">SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="04357-252">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="04357-253">次の Razor ページでは`Counter` 、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="04357-253">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="04357-254">Razor ページとビューからの非対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="04357-254">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="04357-255">次の Razor ページ`MyComponent`では、フォームを使用して指定された初期値を使用して、コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="04357-255">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="04357-256">は`MyComponent`静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="04357-256">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="04357-257">アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="04357-257">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="04357-258">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="04357-258">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="04357-259">場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="04357-259">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="04357-260">たとえば、接続の問題を診断するために、SignalR クライアントのログ記録を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="04357-260">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="04357-261">*Pages/_Host*ファイルで SignalR クライアントを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="04357-261">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="04357-262">*Blazor スクリプト*の`<script>`タグに属性を追加します。`autostart="false"`</span><span class="sxs-lookup"><span data-stu-id="04357-262">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="04357-263">を`Blazor.start`呼び出し、SignalR builder を指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="04357-263">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="04357-264">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="04357-264">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
