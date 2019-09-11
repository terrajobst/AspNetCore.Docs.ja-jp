---
title: ASP.NET Core Blazor のサーバー側アプリをセキュリティで保護する
author: guardrex
description: Blazor サーバー側アプリに対するセキュリティ上の脅威を軽減する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: security/blazor/server-side
ms.openlocfilehash: d30f19bfbbcdb6c142f03a6e0cc6e1fc154c2091
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878525"
---
# <a name="secure-aspnet-core-blazor-server-side-apps"></a><span data-ttu-id="ec9e2-103">ASP.NET Core Blazor のサーバー側アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="ec9e2-103">Secure ASP.NET Core Blazor server-side apps</span></span>

<span data-ttu-id="ec9e2-104">[Javier Calvarro jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ec9e2-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="ec9e2-105">Blazor サーバー側アプリは、*ステートフル*なデータ処理モデルを採用しています。このモデルでは、サーバーとクライアントが長期間の関係を維持しています。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-105">Blazor server-side apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="ec9e2-106">永続的な状態は回線によって維持されます。[回線](xref:blazor/state-management)は、有効期間が長い可能性もある接続にまたがる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="ec9e2-107">ユーザーが Blazor のサーバー側サイトにアクセスすると、サーバーのメモリに回線が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-107">When a user visits a Blazor server-side site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="ec9e2-108">回線は、ユーザーが UI のボタンを選択したときなど、どのコンテンツをレンダリングしてイベントに応答するかをブラウザーに示します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="ec9e2-109">これらのアクションを実行するために、回線は、サーバー上のユーザーのブラウザーと .NET メソッドで JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="ec9e2-110">この双方向の JavaScript ベースの対話は、 [javascript interop (JS interop)](xref:blazor/javascript-interop)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/javascript-interop).</span></span>

<span data-ttu-id="ec9e2-111">JS 相互運用はインターネット経由で行われ、クライアントはリモートブラウザーを使用するため、Blazor のサーバー側アプリは、ほとんどの web アプリのセキュリティに関する懸念事項を共有します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor server-side apps share most web app security concerns.</span></span> <span data-ttu-id="ec9e2-112">このトピックでは、サーバー側アプリを Blazor する一般的な脅威について説明し、インターネットに接続されたアプリに重点を置いた脅威緩和のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-112">This topic describes common threats to Blazor server-side apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="ec9e2-113">企業ネットワークやイントラネット内などの制約された環境では、次のいずれかの軽減ガイダンスがあります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="ec9e2-114">は、制約された環境には適用されません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="ec9e2-115">制約された環境でセキュリティリスクが低いため、を実装する価値はありません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="resource-exhaustion"></a><span data-ttu-id="ec9e2-116">リソース枯渇</span><span class="sxs-lookup"><span data-stu-id="ec9e2-116">Resource exhaustion</span></span>

<span data-ttu-id="ec9e2-117">リソース枯渇は、クライアントがサーバーと通信し、サーバーが過剰なリソースを消費する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-117">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="ec9e2-118">過剰なリソース消費は主に次のように影響します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-118">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="ec9e2-119">CPU</span><span class="sxs-lookup"><span data-stu-id="ec9e2-119">CPU</span></span>](#cpu)
* [<span data-ttu-id="ec9e2-120">メモリ</span><span class="sxs-lookup"><span data-stu-id="ec9e2-120">Memory</span></span>](#memory)
* [<span data-ttu-id="ec9e2-121">クライアント接続</span><span class="sxs-lookup"><span data-stu-id="ec9e2-121">Client connections</span></span>](#client-connections)

<span data-ttu-id="ec9e2-122">サービス拒否 (DoS) 攻撃は、通常、アプリまたはサーバーのリソースを枯渇させることを求めています。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-122">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="ec9e2-123">ただし、リソース枯渇はシステムへの攻撃の結果であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-123">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="ec9e2-124">たとえば、ユーザーの要求が多いため、有限のリソースが使い果たされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-124">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="ec9e2-125">DoS の詳細については、「[サービス拒否 (dos) 攻撃](#denial-of-service-dos-attacks)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-125">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="ec9e2-126">Blazor framework の外部にあるリソース (ファイルの読み取りと書き込みに使用されるデータベースやファイルハンドルなど) も、リソースの枯渇に直面する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-126">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="ec9e2-127">詳細については、「 <xref:performance/performance-best-practices> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-127">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="ec9e2-128">CPU</span><span class="sxs-lookup"><span data-stu-id="ec9e2-128">CPU</span></span>

<span data-ttu-id="ec9e2-129">CPU の枯渇は、1つまたは複数のクライアントが大量の CPU 作業を実行するようサーバーに強制した場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-129">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="ec9e2-130">たとえば、 *Fibonnacci 番号*を計算する Blazor サーバー側アプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-130">For example, consider a Blazor server-side app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="ec9e2-131">Fibonnacci 番号は Fibonnacci シーケンスから生成されます。シーケンス内の各数値は、前の2つの数値の合計です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-131">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="ec9e2-132">回答に必要な作業量は、シーケンスの長さと初期値のサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-132">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="ec9e2-133">アプリがクライアントの要求に制限を設けない場合、CPU 負荷の高い計算で CPU の時間が消費され、他のタスクのパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-133">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="ec9e2-134">過剰なリソース消費は、可用性に影響を与えるセキュリティ上の懸念事項です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-134">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="ec9e2-135">CPU 枯渇は、すべての公開アプリに関する問題です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-135">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="ec9e2-136">通常の web アプリでは、要求と接続は安全としてタイムアウトになりますが、Blazor のサーバー側アプリでは同じセーフガードが提供されません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-136">In regular web apps, requests and connections time out as a safeguard, but Blazor server-side apps don't provide the same safeguards.</span></span> <span data-ttu-id="ec9e2-137">Blazor のサーバー側アプリには、CPU を集中的に使用する作業を実行する前に、適切なチェックと制限が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-137">Blazor server-side apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="ec9e2-138">Memory</span><span class="sxs-lookup"><span data-stu-id="ec9e2-138">Memory</span></span>

<span data-ttu-id="ec9e2-139">メモリ不足は、1つまたは複数のクライアントがサーバーに大量のメモリを強制的に使用する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-139">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="ec9e2-140">たとえば、項目のリストを受け入れて表示するコンポーネントがある Blazor 側アプリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-140">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="ec9e2-141">Blazor アプリで許可される項目の数やクライアントに表示される項目の数に制限がない場合は、メモリを集中的に使用する処理とレンダリングによってサーバーのメモリが消費され、サーバーのパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-141">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="ec9e2-142">サーバーがクラッシュした場合、クラッシュしたと思われる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-142">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="ec9e2-143">サーバー上のメモリ不足のシナリオに関連する項目の一覧を保持および表示するには、次のシナリオを検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-143">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="ec9e2-144">`List<MyItem>`プロパティまたはフィールド内の項目は、サーバーのメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-144">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="ec9e2-145">アプリで項目の一覧を無制限に拡張できる場合は、サーバーでメモリが不足する危険性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-145">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="ec9e2-146">メモリが不足すると、現在のセッションが終了 (クラッシュ) し、そのサーバーインスタンス内のすべての同時実行セッションでメモリ不足の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-146">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="ec9e2-147">このシナリオが発生しないようにするには、同時実行ユーザーにアイテムの制限を課すデータ構造をアプリで使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-147">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="ec9e2-148">ページング方式がレンダリングに使用されていない場合、サーバーは、UI に表示されないオブジェクトに対して追加のメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-148">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="ec9e2-149">項目の数に制限がないと、メモリの需要によって使用可能なサーバーメモリが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-149">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="ec9e2-150">このシナリオを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-150">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="ec9e2-151">レンダリング時に改ページ調整されたリストを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-151">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="ec9e2-152">最初の 100 ~ 1000 項目のみを表示し、表示された項目以外の項目を検索するために検索条件を入力するようユーザーに要求します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-152">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="ec9e2-153">より高度なレンダリングシナリオでは、*仮想化*をサポートするリストまたはグリッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-153">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="ec9e2-154">仮想化を使用すると、一覧に表示されるのは、現在ユーザーに表示されている項目のサブセットのみです。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-154">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="ec9e2-155">UI でユーザーがスクロールバーを操作すると、コンポーネントは表示に必要な項目だけをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-155">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="ec9e2-156">現在、表示に必要とされていない項目は、セカンダリストレージに保持できます。これは理想的な方法です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-156">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="ec9e2-157">記憶されていない項目をメモリに保持することもできますが、これはあまり理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-157">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="ec9e2-158">Blazor サーバー側アプリは、WPF、Windows フォーム、Blazor クライアント側などのステートフルアプリ用の他の UI フレームワークと同様のプログラミングモデルを提供します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-158">Blazor server-side apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor client-side.</span></span> <span data-ttu-id="ec9e2-159">主な違いは、いくつかの UI フレームワークでは、アプリによって消費されるメモリがクライアントに属し、その個々のクライアントのみに影響することです。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-159">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="ec9e2-160">たとえば、Blazor クライアント側アプリはクライアント上で完全に実行され、クライアントメモリリソースのみを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-160">For example, a Blazor client-side app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="ec9e2-161">Blazor サーバー側のシナリオでは、アプリによって消費されるメモリはサーバーに属し、サーバーインスタンス上のクライアント間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-161">In the Blazor server-side scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="ec9e2-162">サーバー側のメモリ要求は、すべてのサーバー側アプリで考慮されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-162">Server-side memory demands are a consideration for all server-side apps.</span></span> <span data-ttu-id="ec9e2-163">ただし、ほとんどの web アプリはステートレスであり、応答が返されると、要求の処理中に使用されるメモリが解放されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-163">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="ec9e2-164">一般的な推奨事項として、クライアント接続を保持する他のサーバー側アプリと同じように、クライアントが非バインドメモリを割り当てられないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-164">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="ec9e2-165">Blazor サーバー側アプリによって消費されるメモリは、1つの要求よりも長い時間保持されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-165">The memory consumed by a Blazor server-side app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="ec9e2-166">開発時には、プロファイラーを使用したり、トレースをキャプチャしてクライアントのメモリ要求を評価したりできます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-166">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="ec9e2-167">プロファイラーまたはトレースは、特定のクライアントに割り当てられたメモリをキャプチャしません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-167">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="ec9e2-168">開発時に特定のクライアントのメモリ使用量をキャプチャするには、ダンプをキャプチャし、ユーザーの回線をルートとするすべてのオブジェクトのメモリ要求を調べます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-168">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="ec9e2-169">クライアント接続</span><span class="sxs-lookup"><span data-stu-id="ec9e2-169">Client connections</span></span>

<span data-ttu-id="ec9e2-170">接続の枯渇は、1つまたは複数のクライアントがサーバーへの同時接続を多数開いていて、他のクライアントが新しい接続を確立できない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-170">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="ec9e2-171">Blazor クライアントは、セッションごとに1つの接続を確立し、ブラウザーウィンドウが開いている間は接続を開いたままにします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-171">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="ec9e2-172">すべての接続を維持するサーバーに対する要求は、Blazor アプリに固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-172">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="ec9e2-173">接続の永続的な性質と Blazor サーバー側アプリのステートフルな特性により、接続の枯渇はアプリの可用性に対するリスクが高くなります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-173">Given the persistent nature of the connections and the stateful nature of Blazor server-side apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="ec9e2-174">既定では、Blazor サーバー側アプリのユーザーあたりの接続数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-174">By default, there's no limit on the number of connections per user for a Blazor server-side app.</span></span> <span data-ttu-id="ec9e2-175">アプリに接続制限が必要な場合は、次の方法の1つまたは複数を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-175">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="ec9e2-176">認証が必要です。これにより、承認されていないユーザーがアプリに接続する機能が自然に制限されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-176">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="ec9e2-177">このシナリオを効果的にするには、ユーザーが新しいユーザーをにプロビジョニングできないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-177">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="ec9e2-178">ユーザーあたりの接続数を制限します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-178">Limit the number of connections per user.</span></span> <span data-ttu-id="ec9e2-179">接続の制限は、次の方法で実現できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-179">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="ec9e2-180">正当なユーザーにアプリへのアクセスを許可します (たとえば、クライアントの IP アドレスに基づいて接続の制限が確立されている場合など)。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-180">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="ec9e2-181">アプリレベル:</span><span class="sxs-lookup"><span data-stu-id="ec9e2-181">At the app level:</span></span>
    * <span data-ttu-id="ec9e2-182">エンドポイントルーティングの拡張性。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-182">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="ec9e2-183">アプリに接続し、ユーザーごとにアクティブなセッションを追跡するには、認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-183">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="ec9e2-184">制限に達したときに新しいセッションを拒否します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-184">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="ec9e2-185">プロキシを使用したアプリへのプロキシ WebSocket 接続 (クライアントからアプリへの接続を多重にする[Azure SignalR サービス](/azure/azure-signalr/signalr-overview)など)。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-185">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="ec9e2-186">これにより、1台のクライアントが確立できるよりも多くの接続容量を備えたアプリが提供されるため、クライアントはサーバーへの接続を使い果たすことができません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-186">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="ec9e2-187">サーバーレベルの場合:アプリの前にプロキシ/ゲートウェイを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-187">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="ec9e2-188">たとえば、 [Azure のフロントドア](/azure/frontdoor/front-door-overview)を使用すると、アプリへの web トラフィックのグローバルルーティングを定義、管理、および監視することができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-188">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="ec9e2-189">サービス拒否 (DoS) 攻撃</span><span class="sxs-lookup"><span data-stu-id="ec9e2-189">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="ec9e2-190">サービス拒否 (DoS) 攻撃では、クライアントによってサーバーが1つ以上のリソースを消費し、アプリを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-190">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="ec9e2-191">Blazor のサーバー側アプリには既定の制限がいくつか含まれており、DoS 攻撃から保護するために他の ASP.NET Core や SignalR の制限に依存しています。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-191">Blazor server-side apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="ec9e2-192">Blazor サーバー側アプリの制限</span><span class="sxs-lookup"><span data-stu-id="ec9e2-192">Blazor server-side app limit</span></span>                            | <span data-ttu-id="ec9e2-193">説明</span><span class="sxs-lookup"><span data-stu-id="ec9e2-193">Description</span></span> | <span data-ttu-id="ec9e2-194">既定値</span><span class="sxs-lookup"><span data-stu-id="ec9e2-194">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="ec9e2-195">特定のサーバーが一度にメモリに保持している切断された回線の最大数。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-195">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="ec9e2-196">100</span><span class="sxs-lookup"><span data-stu-id="ec9e2-196">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="ec9e2-197">切断された回線がメモリに保持されてから破棄されるまでの最大時間。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-197">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="ec9e2-198">3 分</span><span class="sxs-lookup"><span data-stu-id="ec9e2-198">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="ec9e2-199">非同期の JavaScript 関数呼び出しがタイムアウトするまでにサーバーが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-199">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="ec9e2-200">1 分</span><span class="sxs-lookup"><span data-stu-id="ec9e2-200">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="ec9e2-201">信頼性の高い再接続をサポートするために、サーバーが1回線あたりのメモリに保持する未確認のレンダーバッチの最大数。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-201">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="ec9e2-202">制限に達すると、クライアントによって1つ以上のバッチが確認されるまで、サーバーは新しいレンダーバッチの生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-202">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="ec9e2-203">10</span><span class="sxs-lookup"><span data-stu-id="ec9e2-203">10</span></span> |


| <span data-ttu-id="ec9e2-204">SignalR と ASP.NET Core の制限</span><span class="sxs-lookup"><span data-stu-id="ec9e2-204">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="ec9e2-205">説明</span><span class="sxs-lookup"><span data-stu-id="ec9e2-205">Description</span></span> | <span data-ttu-id="ec9e2-206">既定値</span><span class="sxs-lookup"><span data-stu-id="ec9e2-206">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="ec9e2-207">個々のメッセージのメッセージサイズ。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-207">Message size for an individual message.</span></span> | <span data-ttu-id="ec9e2-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="ec9e2-208">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="ec9e2-209">ブラウザー (クライアント) との対話</span><span class="sxs-lookup"><span data-stu-id="ec9e2-209">Interactions with the browser (client)</span></span>

<span data-ttu-id="ec9e2-210">クライアントは、JS 相互運用イベントのディスパッチとレンダリングの完了を通じてサーバーと対話します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-210">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="ec9e2-211">JS 相互運用通信は、JavaScript と .NET の両方の方法で行われます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-211">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="ec9e2-212">ブラウザーイベントは、非同期方式でクライアントからサーバーにディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-212">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="ec9e2-213">サーバーは、必要に応じて UI を非同期に応答します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-213">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="ec9e2-214">.NET から呼び出される JavaScript 関数</span><span class="sxs-lookup"><span data-stu-id="ec9e2-214">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="ec9e2-215">.NET メソッドから JavaScript への呼び出しの場合:</span><span class="sxs-lookup"><span data-stu-id="ec9e2-215">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="ec9e2-216">すべての呼び出し<xref:System.OperationCanceledException>には、失敗してから呼び出し元に返す、構成可能なタイムアウトがあります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-216">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="ec9e2-217">呼び出し (`CircuitOptions.JSInteropDefaultCallTimeout`) の既定のタイムアウトは1分です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-217">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="ec9e2-218">この制限を構成するに<xref:blazor/javascript-interop#harden-js-interop-calls>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-218">To configure this limit, see <xref:blazor/javascript-interop#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="ec9e2-219">キャンセルトークンを指定して、呼び出しごとに取り消しを制御できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-219">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="ec9e2-220">キャンセルトークンが指定されている場合は、可能な限り既定の呼び出しタイムアウトを使用し、クライアントへの呼び出しに時間を制限します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-220">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="ec9e2-221">JavaScript 呼び出しの結果を信頼することはできません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-221">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="ec9e2-222">ブラウザーで実行されている Blazor アプリクライアントは、呼び出す JavaScript 関数を検索します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-222">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="ec9e2-223">関数が呼び出され、結果またはエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-223">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="ec9e2-224">悪意のあるクライアントは次のことを試みることができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-224">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="ec9e2-225">JavaScript 関数からエラーを返すことによって、アプリの問題を発生させます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-225">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="ec9e2-226">JavaScript 関数から予期しない結果を返すことによって、サーバーで意図しない動作を発生させます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-226">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="ec9e2-227">次の予防措置を講じて、上記のシナリオを防止します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-227">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="ec9e2-228">呼び出し中に発生する可能性のあるエラーを考慮するために、 [try catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメント内に JS 相互運用機能呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-228">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="ec9e2-229">詳細については、「 <xref:blazor/handle-errors#javascript-interop> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-229">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="ec9e2-230">アクションを実行する前に、JS 相互運用機能の呼び出しから返されたデータを検証します (エラーメッセージを含む)。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-230">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="ec9e2-231">ブラウザーから呼び出される .NET メソッド</span><span class="sxs-lookup"><span data-stu-id="ec9e2-231">.NET methods invoked from the browser</span></span>

<span data-ttu-id="ec9e2-232">JavaScript から .NET メソッドへの呼び出しを信頼しません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-232">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="ec9e2-233">.NET メソッドが JavaScript に公開されている場合は、.NET メソッドの呼び出し方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-233">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="ec9e2-234">アプリケーションのパブリックエンドポイントと同様に、JavaScript に公開されているすべての .NET メソッドを処理します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-234">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="ec9e2-235">入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-235">Validate input.</span></span>
    * <span data-ttu-id="ec9e2-236">値が想定される範囲内であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-236">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="ec9e2-237">ユーザーが要求された操作を実行するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-237">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="ec9e2-238">.NET メソッドの呼び出しの一部として、過剰な量のリソースを割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-238">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="ec9e2-239">たとえば、チェックを実行し、CPU とメモリの使用量に制限を設けます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-239">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="ec9e2-240">静的メソッドとインスタンスメソッドを JavaScript クライアントに公開できることを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-240">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="ec9e2-241">設計が適切な制約で状態の共有を行う場合を除き、セッション間で状態を共有することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-241">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="ec9e2-242">最初に依存関係の`DotNetReference`挿入 (DI) によって作成されたオブジェクトを介して公開されるメソッドの場合、オブジェクトはスコープ付きオブジェクトとして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-242">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="ec9e2-243">これは、Blazor サーバー側アプリが使用する DI サービスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-243">This applies to any DI service that the Blazor server-side app uses.</span></span>
    * <span data-ttu-id="ec9e2-244">静的メソッドの場合は、アプリがサーバーインスタンス上のすべてのユーザーを対象として状態を明示的に共有している場合を除き、クライアントにスコープを設定できない状態を確立しないようにします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-244">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="ec9e2-245">ユーザーが指定したデータをパラメーターで JavaScript 呼び出しに渡すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-245">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="ec9e2-246">パラメーターにデータを渡す必要がある場合は、JavaScript コードが[クロスサイトスクリプティング (XSS)](#cross-site-scripting-xss)の脆弱性を導入せずにデータを渡すことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-246">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="ec9e2-247">たとえば、要素の`innerHTML`プロパティを設定することによって、ユーザー指定のデータをドキュメントオブジェクトモデル (DOM) に書き込まないでください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-247">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="ec9e2-248">[コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)を使用して`eval` 、およびその他の安全でない JavaScript プリミティブを無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-248">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="ec9e2-249">フレームワークのディスパッチ実装の上に .NET 呼び出しのカスタムディスパッチを実装しないようにします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-249">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="ec9e2-250">ブラウザーに .NET メソッドを公開することは高度なシナリオであり、一般的な Blazor の開発には推奨されません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-250">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="ec9e2-251">イベント</span><span class="sxs-lookup"><span data-stu-id="ec9e2-251">Events</span></span>

<span data-ttu-id="ec9e2-252">イベントは、Blazor サーバー側アプリへのエントリポイントを提供します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-252">Events provide an entry point to a Blazor server-side app.</span></span> <span data-ttu-id="ec9e2-253">Web アプリでエンドポイントを保護する場合と同じ規則が、Blazor サーバー側アプリのイベント処理に適用されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-253">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor server-side apps.</span></span> <span data-ttu-id="ec9e2-254">悪意のあるクライアントは、イベントのペイロードとして送信するデータを送信できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-254">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="ec9e2-255">例えば:</span><span class="sxs-lookup"><span data-stu-id="ec9e2-255">For example:</span></span>

* <span data-ttu-id="ec9e2-256">の変更イベントは、 `<select>`アプリがクライアントに提示したオプションに含まれていない値を送信する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-256">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="ec9e2-257">は`<input>` 、クライアント側の検証をバイパスして、テキストデータをサーバーに送信することができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-257">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="ec9e2-258">アプリは、アプリが処理するイベントのデータを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-258">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="ec9e2-259">Blazor framework[フォームコンポーネント](xref:blazor/forms-validation)は、基本検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-259">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="ec9e2-260">アプリでカスタムフォームコンポーネントを使用する場合は、必要に応じてカスタムコードを記述してイベントデータを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-260">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

<span data-ttu-id="ec9e2-261">Blazor のサーバー側イベントは非同期であるため、新しいレンダリングを生成することによって、アプリが反応するまでの時間を短縮する前に、複数のイベントをサーバーにディスパッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-261">Blazor server-side events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="ec9e2-262">これには、考慮すべきセキュリティ上の影響があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-262">This has some security implications to consider.</span></span> <span data-ttu-id="ec9e2-263">アプリでのクライアントアクションの制限は、現在表示されているビューステートに依存するのではなく、イベントハンドラー内で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-263">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="ec9e2-264">ユーザーがカウンターを最大3回インクリメントできるようにするカウンターコンポーネントを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-264">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="ec9e2-265">カウンターをインクリメントするボタンは、の`count`値に基づいて条件付きで指定します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-265">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```cshtml
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="ec9e2-266">クライアントは、フレームワークがこのコンポーネントの新しいレンダリングを生成する前に、1つまたは複数のインクリメントイベントをディスパッチできます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-266">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="ec9e2-267">結果として、 `count`は UI によって短時間で削除されないので、ユーザーが*3 回以上*インクリメントできることになります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-267">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="ec9e2-268">次の例では、3つ`count`のインクリメントの制限を達成するための正しい方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-268">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```cshtml
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="ec9e2-269">ハンドラー内に`if (count < 3) { ... }`チェックを追加することにより、現在`count`のアプリの状態に基づいてインクリメントするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-269">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="ec9e2-270">この決定は、前の例とは異なり、一時的に古くなっている可能性がある UI の状態に基づいていません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-270">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="ec9e2-271">複数のディスパッチに対して保護する</span><span class="sxs-lookup"><span data-stu-id="ec9e2-271">Guard against multiple dispatches</span></span>

<span data-ttu-id="ec9e2-272">イベントコールバックが、外部サービスまたはデータベースからのデータのフェッチなど、長時間実行される操作を呼び出す場合は、ガードの使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-272">If an event callback invokes a long running operation, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="ec9e2-273">ガードを使用すると、操作の進行中に視覚的なフィードバックが発生している間に、ユーザーが複数の操作をキューに入れることを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-273">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="ec9e2-274">次のコンポーネントコードは`isLoading` 、 `true`が`GetForecastAsync`サーバーからデータを取得するときにをに設定します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-274">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="ec9e2-275">はです`true`が、UI ではボタンが無効になっています。 `isLoading`</span><span class="sxs-lookup"><span data-stu-id="ec9e2-275">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```cshtml
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="ec9e2-276">早期にキャンセルして、dispose を使用しないようにする</span><span class="sxs-lookup"><span data-stu-id="ec9e2-276">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="ec9e2-277">「[複数のディスパッチに対するガード](#guard-against-multiple-dispatches)」で説明されているように、ガードを<xref:System.Threading.CancellationToken>使用するだけでなく、コンポーネントが破棄されたときに、を使用して実行時間の長い操作をキャンセルすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-277">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="ec9e2-278">このアプローチには、コンポーネントでの*dispose の使用*を回避するという追加の利点があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-278">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```cshtml
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        CancellationTokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="ec9e2-279">大量のデータを生成するイベントを回避する</span><span class="sxs-lookup"><span data-stu-id="ec9e2-279">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="ec9e2-280">`oninput` や`onscroll`などの一部の DOM イベントは、大量のデータを生成できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-280">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="ec9e2-281">Blazor server アプリではこれらのイベントを使用しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-281">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="ec9e2-282">セキュリティに関するその他のガイダンス</span><span class="sxs-lookup"><span data-stu-id="ec9e2-282">Additional security guidance</span></span>

<span data-ttu-id="ec9e2-283">ASP.NET Core アプリをセキュリティで保護するためのガイダンスは、Blazor サーバー側アプリに適用されます。詳細については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-283">The guidance for securing ASP.NET Core apps apply to Blazor server-side apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="ec9e2-284">ログ記録と機微なデータ</span><span class="sxs-lookup"><span data-stu-id="ec9e2-284">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="ec9e2-285">HTTPS を使用した転送中の情報の保護</span><span class="sxs-lookup"><span data-stu-id="ec9e2-285">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="ec9e2-286">[クロスサイトスクリプティング (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="ec9e2-286">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="ec9e2-287">クロスオリジン保護</span><span class="sxs-lookup"><span data-stu-id="ec9e2-287">Cross-origin protection</span></span>](#cross-origin-protection)
* <span data-ttu-id="ec9e2-288">[[-Jacking] をクリックします。](#click-jacking)</span><span class="sxs-lookup"><span data-stu-id="ec9e2-288">[Click-jacking](#click-jacking)</span></span>
* [<span data-ttu-id="ec9e2-289">リダイレクトを開く</span><span class="sxs-lookup"><span data-stu-id="ec9e2-289">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="ec9e2-290">ログ記録と機微なデータ</span><span class="sxs-lookup"><span data-stu-id="ec9e2-290">Logging and sensitive data</span></span>

<span data-ttu-id="ec9e2-291">クライアントとサーバー間の JS 相互運用操作は、サーバーのログにインスタンス<xref:Microsoft.Extensions.Logging.ILogger>と共に記録されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-291">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> <span data-ttu-id="ec9e2-292">Blazor は、実際のイベントや JS 相互運用機能の入力や出力などの機密情報のログ記録を回避します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-292">Blazor avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="ec9e2-293">サーバーでエラーが発生すると、フレームワークによってクライアントに通知され、セッションが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-293">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="ec9e2-294">既定では、クライアントは、ブラウザーの開発者ツールに表示される一般的なエラーメッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-294">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="ec9e2-295">クライアント側のエラーには、呼び出し履歴は含まれず、エラーの原因についての詳細は記載されていませんが、サーバーログにはこのような情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-295">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="ec9e2-296">開発目的では、詳細なエラーを有効にすることによって、重要なエラー情報をクライアントで使用できるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-296">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="ec9e2-297">詳細なエラーを有効にする:</span><span class="sxs-lookup"><span data-stu-id="ec9e2-297">Enable detailed errors with:</span></span>

* <span data-ttu-id="ec9e2-298">`CircuitOptions.DetailedErrors`。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-298">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="ec9e2-299">`DetailedErrors`構成キー。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-299">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="ec9e2-300">たとえば、 `ASPNETCORE_DETAILEDERRORS`環境変数をの`true`値に設定します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-300">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ec9e2-301">インターネット上のクライアントにエラー情報を公開することは、常に避ける必要があるセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-301">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="ec9e2-302">HTTPS を使用した転送中の情報の保護</span><span class="sxs-lookup"><span data-stu-id="ec9e2-302">Protect information in transit with HTTPS</span></span>

<span data-ttu-id="ec9e2-303">Blazor は、クライアントとサーバー間の通信に SignalR を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-303">Blazor server-side uses SignalR for communication between the client and the server.</span></span> <span data-ttu-id="ec9e2-304">Blazor サーバー側では、通常、Websocket と SignalR ネゴシエートを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-304">Blazor server-side normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

<span data-ttu-id="ec9e2-305">Blazor サーバー側では、サーバーとクライアントの間で送信されるデータの整合性と機密性は保証されません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-305">Blazor server-side doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="ec9e2-306">常に HTTPS を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-306">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="ec9e2-307">クロスサイトスクリプティング (XSS)</span><span class="sxs-lookup"><span data-stu-id="ec9e2-307">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="ec9e2-308">クロスサイトスクリプティング (XSS) を使用すると、承認されていないパーティは、ブラウザーのコンテキストで任意のロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-308">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="ec9e2-309">侵害されたアプリは、クライアントで任意のコードを実行する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-309">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="ec9e2-310">脆弱性により、サーバーに対していくつかの悪意のある操作が実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-310">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="ec9e2-311">偽/無効なイベントをサーバーにディスパッチします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-311">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="ec9e2-312">ディスパッチの失敗/無効なレンダリング入力候補です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-312">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="ec9e2-313">レンダリング入力候補のディスパッチを回避します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-313">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="ec9e2-314">JavaScript から .NET への相互運用呼び出しをディスパッチします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-314">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="ec9e2-315">.NET から JavaScript への相互運用呼び出しの応答を変更します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-315">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="ec9e2-316">.NET から JS への相互運用の結果をディスパッチしないでください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-316">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="ec9e2-317">Blazor のサーバー側フレームワークは、前述のいくつかの脅威から保護するための手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-317">The Blazor server-side framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="ec9e2-318">クライアントがレンダーバッチを受信確認していない場合、新しい UI 更新の生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-318">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="ec9e2-319">で`CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`構成されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-319">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="ec9e2-320">クライアントからの応答を受信せずに、1分後に .NET から JavaScript への呼び出しをタイムアウトにします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-320">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="ec9e2-321">で`CircuitOptions.JSInteropDefaultCallTimeout`構成されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-321">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="ec9e2-322">JS 相互運用中にブラウザーからのすべての入力に対して基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-322">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="ec9e2-323">.Net 参照は、.NET メソッドによって予期される型と同じです。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-323">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="ec9e2-324">データの形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-324">The data isn't malformed.</span></span>
  * <span data-ttu-id="ec9e2-325">ペイロードには、メソッドの正しい引数数が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-325">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="ec9e2-326">メソッドを呼び出す前に、引数または結果を正しく逆シリアル化できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-326">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="ec9e2-327">次のように、ブラウザーからのすべての入力で、ディスパッチされたイベントから基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-327">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="ec9e2-328">イベントに有効な型があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-328">The event has a valid type.</span></span>
  * <span data-ttu-id="ec9e2-329">イベントのデータを逆シリアル化できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-329">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="ec9e2-330">イベントに関連付けられているイベントハンドラーがあります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-330">There's an event handler associated with the event.</span></span>

<span data-ttu-id="ec9e2-331">フレームワークが実装するセーフガードに加えて、アプリは、脅威から保護し、適切なアクションを実行するために、開発者によってコーディングされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-331">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="ec9e2-332">イベントを処理するときは常にデータを検証します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-332">Always validate data when handling events.</span></span>
* <span data-ttu-id="ec9e2-333">無効なデータの受信時に適切な操作を行います。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-333">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="ec9e2-334">データを無視してを返します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-334">Ignore the data and return.</span></span> <span data-ttu-id="ec9e2-335">これにより、アプリは要求の処理を続行できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-335">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="ec9e2-336">アプリが、入力が違法であり、正当なクライアントによって生成できなかったと判断した場合は、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-336">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="ec9e2-337">例外をスローすると、回線が破棄され、セッションが終了します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-337">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="ec9e2-338">ログに含まれるレンダーバッチの完了によって提供されるエラーメッセージを信頼しません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-338">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="ec9e2-339">このエラーはクライアントによって提供され、通常は信頼できません。クライアントが危害を受ける可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-339">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="ec9e2-340">JavaScript と .NET メソッドの間では、どちらの方向でも、JS 相互運用機能呼び出しの入力を信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-340">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="ec9e2-341">引数または結果が正しく逆シリアル化された場合でも、引数と結果の内容が有効であることを検証するのはアプリの役割です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-341">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="ec9e2-342">XSS の脆弱性が存在するようにするには、レンダリングされたページにユーザー入力を組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-342">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> <span data-ttu-id="ec9e2-343">Blazor サーバー側コンポーネントは、 *razor*ファイル内のマークアップが手続きC#型ロジックに変換されるコンパイル時の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-343">Blazor server-side components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="ec9e2-344">実行時にはC# 、要素、テキスト、および子コンポーネントを記述する*レンダリングツリー*がロジックによって構築されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-344">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="ec9e2-345">これは、JavaScript 命令のシーケンスを使用してブラウザーの DOM に適用されます (または、プリレンダリングの場合は HTML にシリアル化されます)。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-345">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="ec9e2-346">通常の Razor 構文 (など`@someStringValue`) によってレンダリングされるユーザー入力では、テキストのみを書き込むことができるコマンドを使用して DOM に Razor 構文が追加されるため、XSS 脆弱性は公開されません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-346">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="ec9e2-347">値に HTML マークアップが含まれている場合でも、値は静的なテキストとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-347">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="ec9e2-348">プリレンダリングすると、出力は HTML エンコードされ、コンテンツも静的テキストとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-348">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="ec9e2-349">スクリプトタグは許可されていないため、アプリのコンポーネントレンダリングツリーに含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-349">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="ec9e2-350">コンポーネントのマークアップにスクリプトタグが含まれている場合は、コンパイル時エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-350">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="ec9e2-351">コンポーネント作成者は、Razor C#を使用せずにでコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-351">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="ec9e2-352">コンポーネントの作成者は、出力の生成時に適切な Api を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-352">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="ec9e2-353">たとえば、とは`builder.AddContent(0, someUserSuppliedString)`使用し*ませ* `builder.AddMarkupContent(0, someUserSuppliedString)`ん。後者は XSS 脆弱性を作成する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-353">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="ec9e2-354">XSS 攻撃からの保護の一環として、[コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)などの xss 軽減策を実装することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-354">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="ec9e2-355">詳細については、「 <xref:security/cross-site-scripting> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-355">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="ec9e2-356">クロスオリジン保護</span><span class="sxs-lookup"><span data-stu-id="ec9e2-356">Cross-origin protection</span></span>

<span data-ttu-id="ec9e2-357">クロスオリジン攻撃では、サーバーに対してアクションを実行する別のオリジンのクライアントが必要になります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-357">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="ec9e2-358">悪意のあるアクションは通常、GET 要求またはフォームポスト (クロスサイト要求偽造、CSRF) ですが、悪意のある WebSocket を開くことも可能です。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-358">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> <span data-ttu-id="ec9e2-359">Blazor のサーバー側アプリでは、[ハブプロトコルを使用している他の SignalR アプリと同じ保証が](xref:signalr/security)提供されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-359">Blazor server-side apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* <span data-ttu-id="ec9e2-360">Blazor サーバー側アプリは、追加の手段を講じて防ぐことができない限り、クロスオリジンにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-360">Blazor server-side apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="ec9e2-361">クロスオリジンアクセスを無効にするには、エンドポイントで cors を無効にします。そのためには`DisableCorsAttribute` 、パイプラインに cors ミドルウェアを追加し、を Blazor エンドポイントメタデータに追加するか、[クロスオリジンリソースの SignalR を構成して、許可されるオリジンのセットを制限します。共有](xref:signalr/security#cross-origin-resource-sharing)。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-361">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="ec9e2-362">CORS が有効になっている場合、CORS の構成によっては、アプリを保護するために追加の手順が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-362">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="ec9e2-363">Cors がグローバルに有効になっている場合は、を呼び出し`DisableCorsAttribute` `hub.MapBlazorHub()`た後にメタデータをエンドポイントメタデータに追加することで、Blazor サーバー側ハブで cors を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-363">If CORS is globally enabled, CORS can be disabled for the Blazor server-side hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="ec9e2-364">詳細については、「 <xref:security/anti-request-forgery> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-364">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="ec9e2-365">[-Jacking] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-365">Click-jacking</span></span>

<span data-ttu-id="ec9e2-366">[-Jacking] をクリックすると`<iframe>` 、ユーザーが攻撃を受けたサイトでの操作を実行するために、別の配信元のサイトの内部にサイトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-366">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="ec9e2-367">内での`<iframe>`レンダリングからアプリを保護するには、[コンテンツセキュリティポリシー (CSP](https://developer.mozilla.org/docs/Web/HTTP/CSP) `X-Frame-Options` ) とヘッダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-367">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="ec9e2-368">詳細については[、MDN に関する web ドキュメントを参照してください。X-フレームオプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-368">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="ec9e2-369">リダイレクトを開く</span><span class="sxs-lookup"><span data-stu-id="ec9e2-369">Open redirects</span></span>

<span data-ttu-id="ec9e2-370">Blazor のサーバー側アプリセッションが開始されると、サーバーは、セッションの開始の一部として送信される Url の基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-370">When a Blazor server-side app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="ec9e2-371">フレームワークは、回線を確立する前に、ベース URL が現在の URL の親であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-371">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="ec9e2-372">フレームワークでは、追加のチェックは実行されません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-372">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="ec9e2-373">ユーザーがクライアントでリンクを選択すると、リンクの URL がサーバーに送信されます。これにより、実行するアクションが決まります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-373">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="ec9e2-374">たとえば、アプリはクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-374">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="ec9e2-375">コンポーネントは、を使用`NavigationManager`してプログラムでナビゲーション要求をトリガーすることもできます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-375">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="ec9e2-376">このようなシナリオでは、アプリはクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-376">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="ec9e2-377">コンポーネントが必要:</span><span class="sxs-lookup"><span data-stu-id="ec9e2-377">Components must:</span></span>

* <span data-ttu-id="ec9e2-378">ナビゲーション呼び出しの引数の一部としてユーザー入力を使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-378">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="ec9e2-379">引数を検証して、ターゲットがアプリで許可されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-379">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="ec9e2-380">そうしないと、悪意のあるユーザーが、攻撃者によって制御されたサイトに強制的にアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-380">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="ec9e2-381">このシナリオでは、攻撃者は、 `NavigationManager.Navigate`メソッドの呼び出しの一部として、ユーザー入力を使用してアプリに対してトリックを行います。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-381">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="ec9e2-382">このアドバイスは、アプリの一部としてリンクを表示する場合にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-382">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="ec9e2-383">可能であれば、相対リンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-383">If possible, use relative links.</span></span>
* <span data-ttu-id="ec9e2-384">ページに含める前に、絶対リンク先が有効であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-384">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="ec9e2-385">詳細については、「 <xref:security/preventing-open-redirects> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-385">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="ec9e2-386">認証と承認</span><span class="sxs-lookup"><span data-stu-id="ec9e2-386">Authentication and authorization</span></span>

<span data-ttu-id="ec9e2-387">認証と承認のガイダンスについて<xref:security/blazor/index>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-387">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="ec9e2-388">セキュリティチェックリスト</span><span class="sxs-lookup"><span data-stu-id="ec9e2-388">Security checklist</span></span>

<span data-ttu-id="ec9e2-389">次のセキュリティの考慮事項の一覧は、包括的なものではありません。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-389">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="ec9e2-390">イベントから引数を検証します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-390">Validate arguments from events.</span></span>
* <span data-ttu-id="ec9e2-391">JS 相互運用機能呼び出しからの入力と結果を検証します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-391">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="ec9e2-392">.NET から JS への相互運用呼び出しに対して (または事前に検証する) ユーザー入力を使用しないようにします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-392">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="ec9e2-393">クライアントが、バインドされていないメモリの量を割り当てないようにします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-393">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="ec9e2-394">コンポーネント内のデータ。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-394">Data within the component.</span></span>
  * <span data-ttu-id="ec9e2-395">`DotNetObject`クライアントに返される参照。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-395">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="ec9e2-396">複数のディスパッチに対して保護します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-396">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="ec9e2-397">コンポーネントが破棄されるときに、実行時間の長い操作をキャンセルします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-397">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="ec9e2-398">大量のデータを生成するイベントを回避します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-398">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="ec9e2-399">の呼び出し`NavigationManager.Navigate`の一部としてユーザー入力を使用せず、許可されたオリジンのセットに対する url のユーザー入力を、回避できない場合は先に検証します。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-399">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="ec9e2-400">UI の状態に基づいて承認を決定するのではなく、コンポーネントの状態のみを確認してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-400">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="ec9e2-401">[コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)を使用して XSS 攻撃から保護することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-401">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="ec9e2-402">クリック時の確認を防止するには、CSP と[X フレームオプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-402">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="ec9e2-403">Cors を有効にする場合、または Blazor アプリに対して cors を明示的に無効にする場合は、CORS の設定が適切であること</span><span class="sxs-lookup"><span data-stu-id="ec9e2-403">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="ec9e2-404">Blazor アプリのサーバー側の制限が、許容できないレベルのリスクなしで許容できるユーザーエクスペリエンスを提供することをテストします。</span><span class="sxs-lookup"><span data-stu-id="ec9e2-404">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
