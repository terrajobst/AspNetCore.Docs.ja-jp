---
title: ASP.NET Core Blazor 状態管理
author: guardrex
description: Blazor Server アプリで状態を永続化する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2019
uid: blazor/state-management
ms.openlocfilehash: 79676f606d31c435b54bdd8adb1c85c9e5c49bef
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176414"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="df854-103">ASP.NET Core Blazor 状態管理</span><span class="sxs-lookup"><span data-stu-id="df854-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="df854-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="df854-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="df854-105">Blazor Server は、ステートフルアプリフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="df854-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="df854-106">ほとんどの場合、アプリはサーバーへの継続的な接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="df854-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="df854-107">ユーザーの状態は、*回線*内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="df854-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="df854-108">ユーザーの回線に保持されている状態の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="df854-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="df854-109">コンポーネントインスタンスの&mdash;階層と最新のレンダリング出力の UI。</span><span class="sxs-lookup"><span data-stu-id="df854-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="df854-110">コンポーネントインスタンス内のフィールドとプロパティの値。</span><span class="sxs-lookup"><span data-stu-id="df854-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="df854-111">[依存関係の注入 (DI)](xref:fundamentals/dependency-injection)サービスインスタンスに保持されているデータ (回線にスコープされる)。</span><span class="sxs-lookup"><span data-stu-id="df854-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="df854-112">この記事では、Blazor サーバーアプリの状態の永続化について説明します。</span><span class="sxs-lookup"><span data-stu-id="df854-112">This article addresses state persistence in Blazor Server apps.</span></span> <span data-ttu-id="df854-113">Blazor Webasのアプリは[、ブラウザーでのクライアント側の状態の永続](#client-side-in-the-browser)化を利用できますが、この記事の範囲を超えてカスタムソリューションまたはサードパーティのパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="df854-113">Blazor WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a><span data-ttu-id="df854-114">Blazor 回線</span><span class="sxs-lookup"><span data-stu-id="df854-114">Blazor circuits</span></span>

<span data-ttu-id="df854-115">ユーザーが一時的なネットワーク接続の損失を発生した場合、Blazor はユーザーを元の回線に再接続して、アプリを引き続き使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="df854-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="df854-116">ただし、ユーザーをサーバーのメモリ内の元の回線に再接続することは、常に可能であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="df854-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="df854-117">サーバーは切断された回線を永続的に保持できません。</span><span class="sxs-lookup"><span data-stu-id="df854-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="df854-118">サーバーは、タイムアウト後またはサーバーのメモリ不足が発生したときに、切断された回線を解放する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df854-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="df854-119">マルチサーバー、負荷分散された展開環境では、任意の時点でサーバー処理要求が使用できなくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="df854-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="df854-120">要求の全体的な量を処理する必要がなくなった場合は、個々のサーバーに障害が発生するか、自動的に削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="df854-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="df854-121">ユーザーが再接続しようとすると、元のサーバーが使用できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="df854-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="df854-122">ユーザーはブラウザーを閉じて再度開くか、ページを再度読み込んで、ブラウザーのメモリに保持されている状態をすべて削除することができます。</span><span class="sxs-lookup"><span data-stu-id="df854-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="df854-123">たとえば、JavaScript の相互運用呼び出しによって設定された値は失われます。</span><span class="sxs-lookup"><span data-stu-id="df854-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="df854-124">ユーザーが元の回線に再接続できない場合、ユーザーは新しい回線を空の状態で受信します。</span><span class="sxs-lookup"><span data-stu-id="df854-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="df854-125">これは、デスクトップアプリを閉じて再度開くことと同じです。</span><span class="sxs-lookup"><span data-stu-id="df854-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="df854-126">回線間で状態を保持する</span><span class="sxs-lookup"><span data-stu-id="df854-126">Preserve state across circuits</span></span>

<span data-ttu-id="df854-127">場合によっては、回線間で状態を維持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="df854-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="df854-128">次の場合、アプリはユーザーの重要なデータを保持できます。</span><span class="sxs-lookup"><span data-stu-id="df854-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="df854-129">Web サーバーが使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="df854-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="df854-130">ユーザーのブラウザーは、新しい web サーバーで新しい回線を開始することを強制されます。</span><span class="sxs-lookup"><span data-stu-id="df854-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="df854-131">一般に、回線をまたいで状態を維持することは、既に存在するデータを単に読み取るだけではなく、ユーザーが積極的にデータを作成するシナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="df854-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="df854-132">1つの回線を超えて状態を維持するために、*データをサーバーのメモリに格納するだけではいけません*。</span><span class="sxs-lookup"><span data-stu-id="df854-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="df854-133">アプリは、他の保存場所にデータを保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df854-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="df854-134">状態の永続化&mdash;は自動ではありません。アプリケーションを開発してステートフルなデータ永続化を実装するには、手順を実行する必要があります</span><span class="sxs-lookup"><span data-stu-id="df854-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="df854-135">通常、データの永続化は、ユーザーが作成に労力を費やす高価値状態の場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="df854-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="df854-136">次の例では、状態の永続化によって時間が節約されるか、商用活動の支援が行われます。</span><span class="sxs-lookup"><span data-stu-id="df854-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="df854-137">複数の&ndash;手順を追った web フォームでは、ユーザーが状態が失われた場合に、複数の手順で完了したプロセスのデータを再入力するのに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="df854-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="df854-138">このシナリオでは、ユーザーがマルチステップ形式から移動し、後でフォームに戻ると、状態が失われます。</span><span class="sxs-lookup"><span data-stu-id="df854-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="df854-139">ショッピングカート&ndash;は、潜在的な収益を表すアプリの市販の重要なコンポーネントを維持できます。</span><span class="sxs-lookup"><span data-stu-id="df854-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="df854-140">自分の状態やショッピングカートを紛失したユーザーは、後でサイトに戻ったときに、より多くの製品やサービスを購入することができます。</span><span class="sxs-lookup"><span data-stu-id="df854-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="df854-141">通常、送信されていないサインインダイアログに入力したユーザー名など、簡単に再作成された状態を維持する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="df854-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="df854-142">アプリは、アプリの*状態*のみを保持できます。</span><span class="sxs-lookup"><span data-stu-id="df854-142">An app can only persist *app state*.</span></span> <span data-ttu-id="df854-143">コンポーネントインスタンスやそのレンダリングツリーなど、Ui を永続化することはできません。</span><span class="sxs-lookup"><span data-stu-id="df854-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="df854-144">コンポーネントとレンダリングツリーは、一般にシリアル化できません。</span><span class="sxs-lookup"><span data-stu-id="df854-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="df854-145">TreeView の展開されたノードなど、UI の状態に似たものを保持するには、アプリにカスタムコードを用意して、動作を serializable アプリの状態としてモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df854-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="df854-146">状態を保持する場所</span><span class="sxs-lookup"><span data-stu-id="df854-146">Where to persist state</span></span>

<span data-ttu-id="df854-147">Blazor Server アプリで状態を永続化するための一般的な場所は3つあります。</span><span class="sxs-lookup"><span data-stu-id="df854-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="df854-148">各アプローチは、さまざまなシナリオに最適であり、さまざまな注意点があります。</span><span class="sxs-lookup"><span data-stu-id="df854-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="df854-149">データベース内のサーバー側</span><span class="sxs-lookup"><span data-stu-id="df854-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="df854-150">URL</span><span class="sxs-lookup"><span data-stu-id="df854-150">URL</span></span>](#url)
* [<span data-ttu-id="df854-151">クライアント側 (ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="df854-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="df854-152">データベース内のサーバー側</span><span class="sxs-lookup"><span data-stu-id="df854-152">Server-side in a database</span></span>

<span data-ttu-id="df854-153">永続的なデータ永続化や、複数のユーザーまたはデバイスにまたがる必要があるデータについては、独立したサーバー側データベースが最適な選択肢です。</span><span class="sxs-lookup"><span data-stu-id="df854-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="df854-154">次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="df854-154">Options include:</span></span>

* <span data-ttu-id="df854-155">リレーショナル SQL データベース</span><span class="sxs-lookup"><span data-stu-id="df854-155">Relational SQL database</span></span>
* <span data-ttu-id="df854-156">キー/値ストア</span><span class="sxs-lookup"><span data-stu-id="df854-156">Key-value store</span></span>
* <span data-ttu-id="df854-157">Blob ストア</span><span class="sxs-lookup"><span data-stu-id="df854-157">Blob store</span></span>
* <span data-ttu-id="df854-158">テーブルストア</span><span class="sxs-lookup"><span data-stu-id="df854-158">Table store</span></span>

<span data-ttu-id="df854-159">データがデータベースに保存された後は、ユーザーがいつでも新しい回線を開始できます。</span><span class="sxs-lookup"><span data-stu-id="df854-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="df854-160">ユーザーのデータは、すべての新しい回線で保持され、使用できます。</span><span class="sxs-lookup"><span data-stu-id="df854-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="df854-161">Azure のデータストレージオプションの詳細については、 [Azure Storage のドキュメント](/azure/storage/)と[azure データベース](https://azure.microsoft.com/product-categories/databases/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df854-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="df854-162">URL</span><span class="sxs-lookup"><span data-stu-id="df854-162">URL</span></span>

<span data-ttu-id="df854-163">ナビゲーション状態を表す一時的なデータの場合は、URL の一部としてデータをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="df854-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="df854-164">URL でモデル化された状態の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="df854-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="df854-165">表示されているエンティティの ID。</span><span class="sxs-lookup"><span data-stu-id="df854-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="df854-166">ページングされたグリッド内の現在のページ番号。</span><span class="sxs-lookup"><span data-stu-id="df854-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="df854-167">ブラウザーのアドレスバーの内容は保持されます。</span><span class="sxs-lookup"><span data-stu-id="df854-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="df854-168">ユーザーが手動でページを再読み込みした場合。</span><span class="sxs-lookup"><span data-stu-id="df854-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="df854-169">Web サーバーが使用できなく&mdash;なった場合、ユーザーは別のサーバーに接続するために、ページの再読み込みを強制されます。</span><span class="sxs-lookup"><span data-stu-id="df854-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="df854-170">`@page`ディレクティブを使用して URL パターンを定義する方法<xref:blazor/routing>の詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df854-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="df854-171">クライアント側 (ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="df854-171">Client-side in the browser</span></span>

<span data-ttu-id="df854-172">ユーザーがアクティブに作成している一時的なデータの場合、共通のバッキングストア`localStorage`は`sessionStorage` 、ブラウザーのおよびコレクションです。</span><span class="sxs-lookup"><span data-stu-id="df854-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="df854-173">回線が破棄された場合、保存されている状態を管理またはクリアするためにアプリは必要ありません。これは、サーバー側の記憶域よりも利点があります。</span><span class="sxs-lookup"><span data-stu-id="df854-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="df854-174">このセクションの「クライアント側」では、 [Blazor WebAssembly ホスティングモデル](xref:blazor/hosting-models#blazor-webassembly)ではなく、ブラウザーでのクライアント側のシナリオを参照しています。</span><span class="sxs-lookup"><span data-stu-id="df854-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="df854-175">`localStorage`と`sessionStorage`は、カスタムコードを記述したり、サードパーティのパッケージを使用したりするだけで、Blazor webassembly で使用できます。</span><span class="sxs-lookup"><span data-stu-id="df854-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="df854-176">`localStorage`と`sessionStorage`は次のように異なります。</span><span class="sxs-lookup"><span data-stu-id="df854-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="df854-177">`localStorage`は、ユーザーのブラウザーにスコープが設定されています。</span><span class="sxs-lookup"><span data-stu-id="df854-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="df854-178">ユーザーがページを再読み込みするか、ブラウザーを閉じてから再度開くと、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="df854-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="df854-179">ユーザーが複数のブラウザータブを開いた場合、状態はタブ間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="df854-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="df854-180">明示的に`localStorage`クリアされるまで、データはに保持されます。</span><span class="sxs-lookup"><span data-stu-id="df854-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="df854-181">`sessionStorage`は、ユーザーのブラウザータブにスコープが設定されています。ユーザーがタブを再読み込みすると、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="df854-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="df854-182">ユーザーがタブまたはブラウザーを閉じた場合、状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="df854-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="df854-183">ユーザーが複数のブラウザータブを開いた場合、各タブには独立したバージョンのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="df854-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="df854-184">一般に`sessionStorage` 、を使用する方が安全です。</span><span class="sxs-lookup"><span data-stu-id="df854-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="df854-185">`sessionStorage`ユーザーが複数のタブを開いて、次のことが発生するリスクを回避します。</span><span class="sxs-lookup"><span data-stu-id="df854-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="df854-186">タブ間の状態ストレージのバグ。</span><span class="sxs-lookup"><span data-stu-id="df854-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="df854-187">タブが他のタブの状態を上書きするときの動作がわかりにくい。</span><span class="sxs-lookup"><span data-stu-id="df854-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="df854-188">`localStorage`は、ブラウザーを閉じて再度開いている間にアプリが状態を保持する必要がある場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="df854-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="df854-189">ブラウザーの記憶域を使用する場合の注意事項:</span><span class="sxs-lookup"><span data-stu-id="df854-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="df854-190">サーバー側データベースを使用する場合と同様に、データの読み込みと保存は非同期です。</span><span class="sxs-lookup"><span data-stu-id="df854-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="df854-191">サーバー側データベースとは異なり、プリステージ中に要求されたページがブラウザーに存在しないため、プリステージ中にストレージを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="df854-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="df854-192">Blazor Server アプリでは、数キロバイトのデータを保存するのが妥当です。</span><span class="sxs-lookup"><span data-stu-id="df854-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="df854-193">ネットワーク経由でデータが読み込まれ、保存されるため、数キロバイトを超えるパフォーマンスへの影響を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df854-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="df854-194">ユーザーは、データの表示や改ざんを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="df854-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="df854-195">ASP.NET Core[データ保護](xref:security/data-protection/introduction)を使用すると、リスクを軽減できます。</span><span class="sxs-lookup"><span data-stu-id="df854-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="df854-196">サードパーティのブラウザーストレージソリューション</span><span class="sxs-lookup"><span data-stu-id="df854-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="df854-197">サードパーティの NuGet パッケージは`localStorage` 、および`sessionStorage`を操作するための api を提供します。</span><span class="sxs-lookup"><span data-stu-id="df854-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="df854-198">ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を透過的に使用するパッケージを選択することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="df854-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="df854-199">ASP.NET Core データ保護は、格納されているデータを暗号化し、格納されたデータを改ざんするリスクを軽減します。</span><span class="sxs-lookup"><span data-stu-id="df854-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="df854-200">JSON でシリアル化されたデータがプレーンテキストで格納されている場合、ユーザーはブラウザー開発者ツールを使用してデータを表示し、格納されているデータも変更できます。</span><span class="sxs-lookup"><span data-stu-id="df854-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="df854-201">データのセキュリティ保護は、本質的には単純であるため、常に問題になるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="df854-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="df854-202">たとえば、UI 要素に格納されている色の読み取りや変更は、ユーザーや組織にとって重要なセキュリティ上のリスクとは言えません。</span><span class="sxs-lookup"><span data-stu-id="df854-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="df854-203">*機密データ*を検査または改ざんすることをユーザーに許可しないでください。</span><span class="sxs-lookup"><span data-stu-id="df854-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="df854-204">保護されたブラウザーストレージの試験的パッケージ</span><span class="sxs-lookup"><span data-stu-id="df854-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="df854-205">`localStorage` と `sessionStorage` の[データ保護](xref:security/data-protection/introduction)を提供するNuGetパッケージの例は、[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)です。</span><span class="sxs-lookup"><span data-stu-id="df854-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="df854-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`現時点では、サポートされていない実験用パッケージが運用環境で使用するのに適していません。</span><span class="sxs-lookup"><span data-stu-id="df854-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="df854-207">インストール</span><span class="sxs-lookup"><span data-stu-id="df854-207">Installation</span></span>

<span data-ttu-id="df854-208">`Microsoft.AspNetCore.ProtectedBrowserStorage`パッケージをインストールするには:</span><span class="sxs-lookup"><span data-stu-id="df854-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="df854-209">Blazor Server app プロジェクトで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="df854-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="df854-210">最上位レベルの HTML (たとえば、既定のプロジェクトテンプレートの*Pages/_Host*ファイル) で、次`<script>`のタグを追加します。</span><span class="sxs-lookup"><span data-stu-id="df854-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="df854-211">メソッドで、を呼び出し`AddProtectedBrowserStorage`てサービス`localStorage`コレクション`sessionStorage`におよびサービスを追加します。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="df854-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="df854-212">コンポーネント内のデータを保存して読み込む</span><span class="sxs-lookup"><span data-stu-id="df854-212">Save and load data within a component</span></span>

<span data-ttu-id="df854-213">ブラウザーストレージへのデータの読み込みまたは保存を必要とする[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)コンポーネントでは、を使用して、次のいずれかのインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="df854-213">In any component that requires loading or saving data to browser storage, use [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="df854-214">選択は、使用するバッキングストアによって異なります。</span><span class="sxs-lookup"><span data-stu-id="df854-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="df854-215">次の例では`sessionStorage` 、が使用されています。</span><span class="sxs-lookup"><span data-stu-id="df854-215">In the following example, `sessionStorage` is used:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="df854-216">ステートメント`@using`は、コンポーネントではなく、*インポートの razor*ファイルに配置できます。</span><span class="sxs-lookup"><span data-stu-id="df854-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="df854-217">*インポートの razor*ファイルを使用すると、アプリまたはアプリ全体で名前空間を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="df854-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="df854-218">プロジェクトテンプレートの`currentCount` `Counter`コンポーネントに値を保持するには、 `ProtectedSessionStore.SetAsync`次のようにメソッドを変更します。`IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="df854-218">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="df854-219">大規模でより現実的なアプリでは、個々のフィールドを格納するシナリオはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="df854-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="df854-220">アプリは、複雑な状態を含むモデルオブジェクト全体を格納する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="df854-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="df854-221">`ProtectedSessionStore`JSON データを自動的にシリアル化および逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="df854-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="df854-222">前のコード例`currentCount`では、データはとして`sessionStorage['count']`ユーザーのブラウザーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="df854-222">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="df854-223">データはプレーンテキストで保存されるのではなく、ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を使用して保護されます。</span><span class="sxs-lookup"><span data-stu-id="df854-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="df854-224">暗号化されたデータは、 `sessionStorage['count']`ブラウザーの開発者コンソールでが評価された場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="df854-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="df854-225">ユーザーが後`currentCount`で (まったく新しい回線を使用`Counter` `ProtectedSessionStore.GetAsync`しているかどうかを含めて) コンポーネントに戻ったときにデータを回復するには、次のように指定します。</span><span class="sxs-lookup"><span data-stu-id="df854-225">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="df854-226">コンポーネントのパラメーターにナビゲーション状態が含まれて`ProtectedSessionStore.GetAsync`いる場合は、を`OnParametersSetAsync`呼び出し、 `OnInitializedAsync`ではなく結果をに代入します。</span><span class="sxs-lookup"><span data-stu-id="df854-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="df854-227">`OnInitializedAsync`は、コンポーネントが最初にインスタンス化されるときに1回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="df854-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="df854-228">`OnInitializedAsync`は、ユーザーが同じページで別の URL に移動した場合に、後で再度呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="df854-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span>

> [!WARNING]
> <span data-ttu-id="df854-229">このセクションの例は、サーバーのプリレンダリングが有効になっていない場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="df854-229">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="df854-230">プリレンダリングが有効になっていると、次のようなエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="df854-230">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="df854-231">この時点では、JavaScript の相互運用呼び出しは発行できません。</span><span class="sxs-lookup"><span data-stu-id="df854-231">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="df854-232">これは、コンポーネントが prerendered されているためです。</span><span class="sxs-lookup"><span data-stu-id="df854-232">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="df854-233">プリレンダリングを無効にするか、またはプリコーディングを使用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="df854-233">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="df854-234">プリレンダリングで動作するコードの記述の詳細については、「[処理のプリ](#handle-prerendering)コーディング」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df854-234">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="df854-235">読み込み状態を処理する</span><span class="sxs-lookup"><span data-stu-id="df854-235">Handle the loading state</span></span>

<span data-ttu-id="df854-236">ブラウザーストレージは非同期 (ネットワーク接続経由でアクセスされる) であるため、データが読み込まれてコンポーネントで使用できるようになるまでには、常に一定の時間があります。</span><span class="sxs-lookup"><span data-stu-id="df854-236">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="df854-237">最適な結果を得るには、読み込み中に、空白または既定のデータを表示するのではなく、読み込み状態メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="df854-237">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="df854-238">1つの方法は、データが`null` (まだ読み込み中である) かどうかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="df854-238">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="df854-239">既定`Counter`のコンポーネントでは、カウントは`int`に保持されます。</span><span class="sxs-lookup"><span data-stu-id="df854-239">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="df854-240">型`currentCount` `?`()`int`に疑問符 () を追加して null 値を許容するようにします。</span><span class="sxs-lookup"><span data-stu-id="df854-240">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="df854-241">[カウントと**インクリメント**] ボタンを無条件に表示するのではなく、データが読み込まれた場合にのみこれらの要素を表示するように選択します。</span><span class="sxs-lookup"><span data-stu-id="df854-241">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="df854-242">プリレンダリングの処理</span><span class="sxs-lookup"><span data-stu-id="df854-242">Handle prerendering</span></span>

<span data-ttu-id="df854-243">プリレンダリング中:</span><span class="sxs-lookup"><span data-stu-id="df854-243">During prerendering:</span></span>

* <span data-ttu-id="df854-244">ユーザーのブラウザーへの対話型接続は存在しません。</span><span class="sxs-lookup"><span data-stu-id="df854-244">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="df854-245">ブラウザーには、JavaScript コードを実行できるページがまだありません。</span><span class="sxs-lookup"><span data-stu-id="df854-245">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="df854-246">`localStorage`また`sessionStorage`はが、プリレンダリング中に使用できません。</span><span class="sxs-lookup"><span data-stu-id="df854-246">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="df854-247">コンポーネントがストレージを操作しようとすると、次のようなエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="df854-247">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="df854-248">この時点では、JavaScript の相互運用呼び出しは発行できません。</span><span class="sxs-lookup"><span data-stu-id="df854-248">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="df854-249">これは、コンポーネントが prerendered されているためです。</span><span class="sxs-lookup"><span data-stu-id="df854-249">This is because the component is being prerendered.</span></span>

<span data-ttu-id="df854-250">このエラーを解決する方法の1つは、プリレンダリングを無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="df854-250">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="df854-251">これは通常、アプリでブラウザーベースのストレージを多用する場合に最適な選択肢です。</span><span class="sxs-lookup"><span data-stu-id="df854-251">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="df854-252">プリレンダリングによって複雑さが`localStorage`増し、アプリには`sessionStorage`メリットがありません</span><span class="sxs-lookup"><span data-stu-id="df854-252">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="df854-253">プリレンダリングを無効にするには、 *Pages/_Host*ファイルを開き、の`Html.RenderComponentAsync<App>(RenderMode.Server)`呼び出しを変更します。</span><span class="sxs-lookup"><span data-stu-id="df854-253">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `Html.RenderComponentAsync<App>(RenderMode.Server)`.</span></span>

<span data-ttu-id="df854-254">またはを使用`localStorage`しない他のページでは`sessionStorage`、プリレンダリングが役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="df854-254">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="df854-255">プリレンダリングが有効な状態を維持するには、ブラウザーが回線に接続されるまで読み込み操作を延期します。</span><span class="sxs-lookup"><span data-stu-id="df854-255">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="df854-256">次に、カウンター値を格納する例を示します。</span><span class="sxs-lookup"><span data-stu-id="df854-256">The following is an example for storing a counter value:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="df854-257">共通の場所に保持される状態を考慮する</span><span class="sxs-lookup"><span data-stu-id="df854-257">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="df854-258">多くのコンポーネントがブラウザーベースのストレージに依存している場合は、状態プロバイダーコードを何度も再実装すると、コードの重複が発生します。</span><span class="sxs-lookup"><span data-stu-id="df854-258">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="df854-259">コードの重複を回避するためのオプションの1つは、状態プロバイダーのロジックをカプセル化する*状態プロバイダーの親コンポーネント*を作成することです。</span><span class="sxs-lookup"><span data-stu-id="df854-259">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="df854-260">子コンポーネントは、状態の永続化メカニズムに関係なく、永続化されたデータを処理できます。</span><span class="sxs-lookup"><span data-stu-id="df854-260">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="df854-261">次の`CounterStateProvider`コンポーネントの例では、カウンターデータが永続化されます。</span><span class="sxs-lookup"><span data-stu-id="df854-261">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="df854-262">読み込み`CounterStateProvider`が完了するまで子コンテンツをレンダリングしないことで、コンポーネントは読み込みフェーズを処理します。</span><span class="sxs-lookup"><span data-stu-id="df854-262">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="df854-263">`CounterStateProvider`コンポーネントを使用するには、カウンターの状態へのアクセスを必要とする他のコンポーネントの周囲にコンポーネントのインスタンスをラップします。</span><span class="sxs-lookup"><span data-stu-id="df854-263">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="df854-264">アプリ内の`CounterStateProvider`すべての`Router` `App`コンポーネントから状態にアクセスできるようにするには、コンポーネント (*app.xaml*) のを周囲にコンポーネントをラップします。</span><span class="sxs-lookup"><span data-stu-id="df854-264">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="df854-265">ラップされたコンポーネントはを受け取り、永続化されたカウンターの状態を変更できます。</span><span class="sxs-lookup"><span data-stu-id="df854-265">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="df854-266">次`Counter`のコンポーネントは、というパターンを実装しています。</span><span class="sxs-lookup"><span data-stu-id="df854-266">The following `Counter` component implements the pattern:</span></span>

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="df854-267">前のコンポーネントは、との`ProtectedBrowserStorage`対話には必要ありません。また、"読み込み中" フェーズも処理しません。</span><span class="sxs-lookup"><span data-stu-id="df854-267">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="df854-268">前に説明したように、 `CounterStateProvider`プリレンダリングを処理するには、カウンターデータを使用するすべてのコンポーネントがプリレンダリングで自動的に動作するように、を修正できます。</span><span class="sxs-lookup"><span data-stu-id="df854-268">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="df854-269">詳細については、「[ハンドルのプリレンダリング](#handle-prerendering)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df854-269">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="df854-270">一般に、*状態プロバイダーの親コンポーネント*パターンをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="df854-270">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="df854-271">他の多くのコンポーネントで状態を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="df854-271">To consume state in many other components.</span></span>
* <span data-ttu-id="df854-272">保持する最上位レベルの状態オブジェクトが1つだけの場合。</span><span class="sxs-lookup"><span data-stu-id="df854-272">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="df854-273">さまざまな状態オブジェクトを保持し、異なる場所にあるオブジェクトの異なるサブセットを使用するには、状態の読み込みと保存をグローバルに処理しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="df854-273">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
