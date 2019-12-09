---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor Webasと Blazor サーバーホスティングモデルについて理解します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7676d16bddf146ea38619ed35c5e32c5bce731de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943767"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="05232-103">Blazor ホスティングモデルの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05232-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="05232-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="05232-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="05232-105"> は、ブラウザーでブラウザーでクライアント側を実行するために設計された web フレームワークであり、 [webasに](https://webassembly.org/)基づく .net ランタイム ( *Blazor webassembly*、または ASP.NET Core ( *Blazor サーバー*) のサーバー側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="05232-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="05232-106">ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。</span><span class="sxs-lookup"><span data-stu-id="05232-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="05232-107">この記事で説明されているホスティングモデルのプロジェクトを作成するには、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="05232-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="05232-108"> WebAssembly</span></span>

<span data-ttu-id="05232-109">Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="05232-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="05232-110">Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="05232-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="05232-111">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="05232-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="05232-112">UI の更新とイベントの処理は、同じプロセス内で行われます。</span><span class="sxs-lookup"><span data-stu-id="05232-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="05232-113">アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。</span><span class="sxs-lookup"><span data-stu-id="05232-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[!ファンド.NO LOC (Blazor)]<span data-ttu-id="05232-114"> WebAssembly [!ファンド.NO LOC (Blazor)] アプリは、ブラウザー内の UI スレッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="05232-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="05232-115">クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="05232-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="05232-116">**Blazor WebAssembly**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([dotnet new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="05232-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="05232-117">ASP.NET Core アプリは、クライアントに対して Blazor アプリを提供します。</span><span class="sxs-lookup"><span data-stu-id="05232-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="05232-118">Blazor WebAssembly は、web API 呼び出しまたは[SignalR](xref:signalr/introduction)を使用して、ネットワーク経由でサーバーと通信できます。</span><span class="sxs-lookup"><span data-stu-id="05232-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="05232-119">テンプレートには、を処理する*blazor*スクリプトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="05232-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="05232-120">.NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。</span><span class="sxs-lookup"><span data-stu-id="05232-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="05232-121">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="05232-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="05232-122">Blazor WebAssembly ホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="05232-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="05232-123">.NET サーバー側の依存関係はありません。</span><span class="sxs-lookup"><span data-stu-id="05232-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="05232-124">クライアントにダウンロードされた後、アプリは完全に機能しています。</span><span class="sxs-lookup"><span data-stu-id="05232-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="05232-125">クライアントのリソースと機能は完全に活用されています。</span><span class="sxs-lookup"><span data-stu-id="05232-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="05232-126">作業はサーバーからクライアントにオフロードされます。</span><span class="sxs-lookup"><span data-stu-id="05232-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="05232-127">アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="05232-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="05232-128">サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="05232-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="05232-129">Blazor Webasホストには欠点があります。</span><span class="sxs-lookup"><span data-stu-id="05232-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="05232-130">アプリは、ブラウザーの機能に制限されています。</span><span class="sxs-lookup"><span data-stu-id="05232-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="05232-131">サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。</span><span class="sxs-lookup"><span data-stu-id="05232-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="05232-132">ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="05232-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="05232-133">.NET ランタイムとツールのサポートの成熟度は低くなります。</span><span class="sxs-lookup"><span data-stu-id="05232-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="05232-134">たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。</span><span class="sxs-lookup"><span data-stu-id="05232-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="05232-135"> サーバー</span><span class="sxs-lookup"><span data-stu-id="05232-135"> Server</span></span>

<span data-ttu-id="05232-136">Blazor サーバーホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="05232-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="05232-137">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="05232-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ブラウザーは、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。ファンド.NO LOC (SignalR)] 接続。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="05232-139">Blazor Server ホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor サーバーアプリ**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="05232-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="05232-140">ASP.NET Core アプリは、Blazor Server アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="05232-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="05232-141">ASP.NET Core アプリは、追加するアプリの `Startup` クラスを参照します。</span><span class="sxs-lookup"><span data-stu-id="05232-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="05232-142">サーバー側サービス。</span><span class="sxs-lookup"><span data-stu-id="05232-142">Server-side services.</span></span>
* <span data-ttu-id="05232-143">要求処理パイプラインに対するアプリ。</span><span class="sxs-lookup"><span data-stu-id="05232-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="05232-144">*Blazor*スクリプト&dagger; によって、クライアント接続が確立されます。</span><span class="sxs-lookup"><span data-stu-id="05232-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="05232-145">アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。</span><span class="sxs-lookup"><span data-stu-id="05232-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="05232-146">Blazor サーバーホスティングモデルには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="05232-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="05232-147">ダウンロードサイズは Blazor Webasapp よりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="05232-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="05232-148">このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="05232-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="05232-149">サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。</span><span class="sxs-lookup"><span data-stu-id="05232-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="05232-150">シンクライアントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="05232-150">Thin clients are supported.</span></span> <span data-ttu-id="05232-151">たとえば、Blazor サーバーアプリは、WebAssembly サポートされていないブラウザーや、リソースが制限されたデバイスで動作します。</span><span class="sxs-lookup"><span data-stu-id="05232-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="05232-152">アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。</span><span class="sxs-lookup"><span data-stu-id="05232-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="05232-153">Blazor サーバーのホストには欠点があります。</span><span class="sxs-lookup"><span data-stu-id="05232-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="05232-154">通常、待機時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="05232-154">Higher latency usually exists.</span></span> <span data-ttu-id="05232-155">すべてのユーザーの操作には、ネットワークホップが関係します。</span><span class="sxs-lookup"><span data-stu-id="05232-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="05232-156">オフラインサポートはありません。</span><span class="sxs-lookup"><span data-stu-id="05232-156">There's no offline support.</span></span> <span data-ttu-id="05232-157">クライアント接続が失敗した場合、アプリは動作を停止します。</span><span class="sxs-lookup"><span data-stu-id="05232-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="05232-158">多くのユーザーがいるアプリでは、スケーラビリティが困難です。</span><span class="sxs-lookup"><span data-stu-id="05232-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="05232-159">サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="05232-160">アプリを提供するには、ASP.NET Core サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="05232-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="05232-161">サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。</span><span class="sxs-lookup"><span data-stu-id="05232-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="05232-162">&dagger;、 *blazor*スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。</span><span class="sxs-lookup"><span data-stu-id="05232-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="05232-163">サーバーレンダリングの UI との比較</span><span class="sxs-lookup"><span data-stu-id="05232-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="05232-164">Blazor サーバーアプリを理解する方法の1つは、Razor ビューまたは Razor Pages を使用する ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。</span><span class="sxs-lookup"><span data-stu-id="05232-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="05232-165">どちらのモデルでも、Razor 言語を使用して HTML コンテンツを記述しますが、マークアップのレンダリング方法が大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="05232-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="05232-166">Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。</span><span class="sxs-lookup"><span data-stu-id="05232-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="05232-167">レンダリング後、サーバーは、生成されたすべての状態を含むページまたはビューインスタンスを破棄します。</span><span class="sxs-lookup"><span data-stu-id="05232-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="05232-168">サーバーの検証に失敗し、検証の概要が表示される場合など、ページに対する別の要求が発生したとき。</span><span class="sxs-lookup"><span data-stu-id="05232-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="05232-169">ページ全体が HTML テキストに再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="05232-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="05232-170">ページがクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="05232-170">The page is sent to the client.</span></span>

<span data-ttu-id="05232-171">Blazor アプリは、*コンポーネント*と呼ばれる UI の再利用可能な要素で構成されます。</span><span class="sxs-lookup"><span data-stu-id="05232-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="05232-172">コンポーネントにはC# 、コード、マークアップ、およびその他のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="05232-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="05232-173">コンポーネントがレンダリングされると、Blazor は HTML または XML ドキュメントオブジェクトモデル (DOM) のような、含まれているコンポーネントのグラフを生成します。</span><span class="sxs-lookup"><span data-stu-id="05232-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="05232-174">このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="05232-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="05232-175"> は、コンポーネントグラフを評価して、マークアップのバイナリ表現を生成します。</span><span class="sxs-lookup"><span data-stu-id="05232-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="05232-176">バイナリ形式は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="05232-176">The binary format can be:</span></span>

* <span data-ttu-id="05232-177">(プリレンダリング中に) HTML テキストに変換されます。</span><span class="sxs-lookup"><span data-stu-id="05232-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="05232-178">通常のレンダリング中にマークアップを効率的に更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05232-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="05232-179">Blazor の UI の更新は、次の方法でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="05232-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="05232-180">ユーザー操作 (ボタンの選択など)。</span><span class="sxs-lookup"><span data-stu-id="05232-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="05232-181">タイマーなどのアプリトリガー。</span><span class="sxs-lookup"><span data-stu-id="05232-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="05232-182">グラフが再ピアリングされ、UI *diff* (差分) が計算されます。</span><span class="sxs-lookup"><span data-stu-id="05232-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="05232-183">この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。</span><span class="sxs-lookup"><span data-stu-id="05232-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="05232-184">Diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="05232-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="05232-185">コンポーネントは、ユーザーがクライアント上で移動した後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="05232-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="05232-186">ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) は、サーバーのメモリに保持されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="05232-187">多くのコンポーネントの状態は同時にサーバーによって維持される可能性があるため、メモリ不足に対処する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="05232-188">サーバーメモリを最大限に活用するために Blazor Server アプリを作成する方法については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="05232-189">接続</span><span class="sxs-lookup"><span data-stu-id="05232-189">Circuits</span></span>

<span data-ttu-id="05232-190">Blazor サーバーアプリは[ASP.NET Core SignalR](xref:signalr/introduction)の上に構築されています。</span><span class="sxs-lookup"><span data-stu-id="05232-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="05232-191">各クライアントは、*回線*と呼ばれる1つ以上の SignalR 接続を介してサーバーと通信します。</span><span class="sxs-lookup"><span data-stu-id="05232-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="05232-192">回線は、一時的なネットワーク中断が許容される SignalR 接続に対して Blazor抽象化されています。</span><span class="sxs-lookup"><span data-stu-id="05232-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="05232-193">Blazor クライアントは、SignalR 接続が切断されていることを確認すると、新しい SignalR 接続を使用してサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="05232-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="05232-194">Blazor Server アプリに接続されている各ブラウザー画面 (ブラウザータブまたは iframe) は、SignalR 接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="05232-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="05232-195">これは、サーバーでレンダリングされる一般的なアプリと比較して、もう1つ重要な違いです。</span><span class="sxs-lookup"><span data-stu-id="05232-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="05232-196">サーバー側でレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開くのは、通常、サーバーに対する追加のリソース要求には変換されません。</span><span class="sxs-lookup"><span data-stu-id="05232-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="05232-197">Blazor サーバーアプリでは、各ブラウザー画面に個別の回線が必要で、コンポーネント状態の個別のインスタンスがサーバーによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="05232-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="05232-198"> は、ブラウザータブを閉じるか、外部 URL に移動して*正常*に終了すると見なされます。</span><span class="sxs-lookup"><span data-stu-id="05232-198"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="05232-199">正常な終了が発生した場合、回線と関連するリソースが直ちに解放されます。</span><span class="sxs-lookup"><span data-stu-id="05232-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="05232-200">クライアントは、ネットワークの中断などによって、正常に切断されることもあります。</span><span class="sxs-lookup"><span data-stu-id="05232-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="05232-201"> サーバーは、クライアントが再接続できるように、接続されていない回線を構成可能な間隔で格納します。</span><span class="sxs-lookup"><span data-stu-id="05232-201"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="05232-202">詳細については、「[同じサーバーへ](#reconnection-to-the-same-server)の再接続」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="05232-203">UI の待機時間</span><span class="sxs-lookup"><span data-stu-id="05232-203">UI Latency</span></span>

<span data-ttu-id="05232-204">UI 待機時間とは、開始されたアクションから UI が更新されるまでにかかる時間のことです。</span><span class="sxs-lookup"><span data-stu-id="05232-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="05232-205">アプリがユーザーに応答できるようにするには、UI の待機時間の値を小さくすることが不可欠です。</span><span class="sxs-lookup"><span data-stu-id="05232-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="05232-206">Blazor Server アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。</span><span class="sxs-lookup"><span data-stu-id="05232-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="05232-207">その結果、UI の待機時間は、ネットワーク待機時間の合計と、アクションを処理するサーバーの待機時間です。</span><span class="sxs-lookup"><span data-stu-id="05232-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="05232-208">企業のプライベートネットワークに限定された基幹業務アプリの場合、ネットワーク待機時間による待ち時間のユーザーへの影響は、通常はなるべくです。</span><span class="sxs-lookup"><span data-stu-id="05232-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="05232-209">インターネット経由で展開されたアプリの場合、ユーザーにとって待機時間が顕著になる可能性があります。ユーザーが地理的に広く分散している場合は特にそうです。</span><span class="sxs-lookup"><span data-stu-id="05232-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="05232-210">メモリ使用量は、アプリの待機時間に寄与する場合もあります。</span><span class="sxs-lookup"><span data-stu-id="05232-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="05232-211">メモリ使用量が増加すると、ガベージコレクションまたはメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI の遅延が増加します。</span><span class="sxs-lookup"><span data-stu-id="05232-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="05232-212">詳細については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-212">For more information, see <xref:security/blazor/server>.</span></span>

Blazor<span data-ttu-id="05232-213"> サーバーアプリは、ネットワーク待機時間とメモリ使用量を削減することで、UI の待機時間を最小限に抑えるように最適化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-213"> Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="05232-214">ネットワーク待機時間を測定する方法については、「<xref:host-and-deploy/blazor/server#measure-network-latency>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="05232-215">SignalR と Blazorの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="05232-216">サーバーへの接続</span><span class="sxs-lookup"><span data-stu-id="05232-216">Connection to the server</span></span>

Blazor<span data-ttu-id="05232-217"> サーバーアプリには、サーバーへのアクティブな SignalR 接続が必要です。</span><span class="sxs-lookup"><span data-stu-id="05232-217"> Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="05232-218">接続が失われた場合、アプリはサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="05232-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="05232-219">クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。</span><span class="sxs-lookup"><span data-stu-id="05232-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="05232-220">同じサーバーへの再接続</span><span class="sxs-lookup"><span data-stu-id="05232-220">Reconnection to the same server</span></span>

<span data-ttu-id="05232-221">最初のクライアント要求への応答としての Blazor Server アプリ prerenders。これにより、サーバー上で UI の状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="05232-221">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="05232-222">クライアントが SignalR 接続を作成しようとすると、クライアントは同じサーバーに再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-222">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="05232-223">複数のバックエンドサーバーを使用する Blazor サーバーアプリは、SignalR 接続用の*固定セッション*を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-223">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="05232-224">Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="05232-224">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="05232-225">このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="05232-225">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="05232-226">Azure SignalR サービスでは、サービスの `ServerStickyMode` オプションまたは構成値を `Required`に設定することにより、固定セッションが有効になります。</span><span class="sxs-lookup"><span data-stu-id="05232-226">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="05232-227">詳細については、「<xref:host-and-deploy/blazor/server#signalr-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05232-227">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="05232-228">IIS を使用すると、スティッキー セッションはアプリケーション要求ルーティングによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="05232-228">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="05232-229">詳しくは、「[アプリケーション要求ルーティングを使用した HTTP 負荷分散](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05232-229">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="05232-230">UI の接続状態を反映します。</span><span class="sxs-lookup"><span data-stu-id="05232-230">Reflect the connection state in the UI</span></span>

<span data-ttu-id="05232-231">クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="05232-231">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="05232-232">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="05232-232">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="05232-233">UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="05232-233">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="05232-234">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="05232-234">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="05232-235">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="05232-235">CSS class</span></span>                       | <span data-ttu-id="05232-236">&hellip; を示します。</span><span class="sxs-lookup"><span data-stu-id="05232-236">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="05232-237">失われた接続。</span><span class="sxs-lookup"><span data-stu-id="05232-237">A lost connection.</span></span> <span data-ttu-id="05232-238">クライアントが再接続しようとしています。</span><span class="sxs-lookup"><span data-stu-id="05232-238">The client is attempting to reconnect.</span></span> <span data-ttu-id="05232-239">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="05232-239">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="05232-240">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="05232-240">An active connection is re-established to the server.</span></span> <span data-ttu-id="05232-241">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="05232-241">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="05232-242">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05232-242">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="05232-243">再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05232-243">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="05232-244">再接続は拒否されました。</span><span class="sxs-lookup"><span data-stu-id="05232-244">Reconnection rejected.</span></span> <span data-ttu-id="05232-245">サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。</span><span class="sxs-lookup"><span data-stu-id="05232-245">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="05232-246">アプリを再度読み込むには、`location.reload()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="05232-246">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="05232-247">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05232-247">This connection state may result when:</span></span><ul><li><span data-ttu-id="05232-248">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="05232-248">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="05232-249">サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。</span><span class="sxs-lookup"><span data-stu-id="05232-249">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="05232-250">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="05232-250">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="05232-251">サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</span><span class="sxs-lookup"><span data-stu-id="05232-251">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="05232-252">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="05232-252">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="05232-253">サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために、Blazor サーバーアプリが既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="05232-253">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="05232-254">これは *_Host. cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="05232-254">This is set up in the *_Host.cshtml* Razor page:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

<span data-ttu-id="05232-255">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="05232-255">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="05232-256">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="05232-256">Is prerendered into the page.</span></span>
* <span data-ttu-id="05232-257">は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="05232-257">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="05232-258">説明</span><span class="sxs-lookup"><span data-stu-id="05232-258">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="05232-259">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="05232-259">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="05232-260">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05232-260">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="05232-261">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="05232-261">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="05232-262">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="05232-262">Output from the component isn't included.</span></span> <span data-ttu-id="05232-263">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05232-263">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="05232-264">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="05232-264">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="05232-265">説明</span><span class="sxs-lookup"><span data-stu-id="05232-265">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="05232-266">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="05232-266">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="05232-267">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05232-267">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="05232-268">パラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="05232-268">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="05232-269">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="05232-269">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="05232-270">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="05232-270">Output from the component isn't included.</span></span> <span data-ttu-id="05232-271">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="05232-271">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="05232-272">パラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="05232-272">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="05232-273">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="05232-273">Renders the component into static HTML.</span></span> <span data-ttu-id="05232-274">パラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="05232-274">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="05232-275">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="05232-275">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="05232-276">`RenderMode` が `ServerPrerendered`場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="05232-276">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="05232-277">ブラウザーがサーバーへの接続を確立すると、コンポーネントが*再び*表示され、コンポーネントが対話型になります。</span><span class="sxs-lookup"><span data-stu-id="05232-277">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="05232-278">コンポーネントを初期化するための[Oninitialized 化された {Async}](xref:blazor/lifecycle#component-initialization-methods)ライフサイクルメソッドが存在する場合、メソッドは*2 回*実行されます。</span><span class="sxs-lookup"><span data-stu-id="05232-278">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="05232-279">コンポーネントが静的に prerendered された場合。</span><span class="sxs-lookup"><span data-stu-id="05232-279">When the component is prerendered statically.</span></span>
* <span data-ttu-id="05232-280">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="05232-280">After the server connection has been established.</span></span>

<span data-ttu-id="05232-281">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変化する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05232-281">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="05232-282">Blazor サーバーアプリでダブルレンダリングのシナリオを回避するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="05232-282">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="05232-283">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="05232-283">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="05232-284">コンポーネントの状態を保存するには、プリレンダリング時に識別子を使用します。</span><span class="sxs-lookup"><span data-stu-id="05232-284">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="05232-285">プリレンダリング後に識別子を使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="05232-285">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="05232-286">次のコードは、テンプレートベースの Blazor サーバーアプリで、ダブルレンダリングを回避する更新された `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="05232-286">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
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
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="05232-287">Razor ページとビューからのステートフル対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="05232-287">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="05232-288">ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。</span><span class="sxs-lookup"><span data-stu-id="05232-288">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="05232-289">ページまたはビューが表示される場合:</span><span class="sxs-lookup"><span data-stu-id="05232-289">When the page or view renders:</span></span>

* <span data-ttu-id="05232-290">コンポーネントは、ページまたはビューと prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="05232-290">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="05232-291">プリレンダリングに使用される初期コンポーネントの状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="05232-291">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="05232-292">SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="05232-292">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="05232-293">次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="05232-293">The following Razor page renders a `Counter` component:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="05232-294">Razor ページとビューからの非対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="05232-294">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="05232-295">次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="05232-295">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

::: moniker range=">= aspnetcore-3.1"

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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<Counter>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

<span data-ttu-id="05232-296">`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="05232-296">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="05232-297">アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="05232-297">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="05232-298">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="05232-298">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="05232-299">場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05232-299">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="05232-300">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="05232-300">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="05232-301">*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="05232-301">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="05232-302">*Blazor*スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="05232-302">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="05232-303">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="05232-303">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="05232-304">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="05232-304">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
