---
title: Blazor 状態管理の ASP.NET Core
author: guardrex
description: Blazor サーバーアプリで状態を永続化する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/state-management
ms.openlocfilehash: 38ee5fccdf476f08c9f39d01b53c81b48eea04bf
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317181"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="310df-103">Blazor 状態管理の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="310df-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="310df-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="310df-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="310df-105"> Server は、ステートフルアプリフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="310df-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="310df-106">ほとんどの場合、アプリはサーバーへの継続的な接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="310df-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="310df-107">ユーザーの状態は、*回線*内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="310df-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="310df-108">ユーザーの回線に保持されている状態の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="310df-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="310df-109">レンダリングされた UI&mdash;、コンポーネントインスタンスの階層と最新のレンダリング出力です。</span><span class="sxs-lookup"><span data-stu-id="310df-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="310df-110">コンポーネントインスタンス内のフィールドとプロパティの値。</span><span class="sxs-lookup"><span data-stu-id="310df-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="310df-111">[依存関係の注入 (DI)](xref:fundamentals/dependency-injection)サービスインスタンスに保持されているデータ (回線にスコープされる)。</span><span class="sxs-lookup"><span data-stu-id="310df-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="310df-112">この記事では Blazor サーバーアプリの状態の永続化について説明します。</span><span class="sxs-lookup"><span data-stu-id="310df-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="310df-113"> WebAssembly は[、ブラウザーでのクライアント側の状態の永続](#client-side-in-the-browser)化を利用できますが、この記事の範囲を超えたカスタムソリューションまたはサードパーティのパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="310df-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="310df-114"> 回線</span><span class="sxs-lookup"><span data-stu-id="310df-114"> circuits</span></span>

<span data-ttu-id="310df-115">ユーザーが一時的なネットワーク接続の損失を発生した場合、Blazor はユーザーを元の回線に再接続しようとして、アプリを引き続き使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="310df-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="310df-116">ただし、ユーザーをサーバーのメモリ内の元の回線に再接続することは、常に可能であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="310df-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="310df-117">サーバーは切断された回線を永続的に保持できません。</span><span class="sxs-lookup"><span data-stu-id="310df-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="310df-118">サーバーは、タイムアウト後またはサーバーのメモリ不足が発生したときに、切断された回線を解放する必要があります。</span><span class="sxs-lookup"><span data-stu-id="310df-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="310df-119">マルチサーバー、負荷分散された展開環境では、任意の時点でサーバー処理要求が使用できなくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="310df-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="310df-120">要求の全体的な量を処理する必要がなくなった場合は、個々のサーバーに障害が発生するか、自動的に削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="310df-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="310df-121">ユーザーが再接続しようとすると、元のサーバーが使用できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="310df-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="310df-122">ユーザーはブラウザーを閉じて再度開くか、ページを再度読み込んで、ブラウザーのメモリに保持されている状態をすべて削除することができます。</span><span class="sxs-lookup"><span data-stu-id="310df-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="310df-123">たとえば、JavaScript の相互運用呼び出しによって設定された値は失われます。</span><span class="sxs-lookup"><span data-stu-id="310df-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="310df-124">ユーザーが元の回線に再接続できない場合、ユーザーは新しい回線を空の状態で受信します。</span><span class="sxs-lookup"><span data-stu-id="310df-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="310df-125">これは、デスクトップアプリを閉じて再度開くことと同じです。</span><span class="sxs-lookup"><span data-stu-id="310df-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="310df-126">回線間で状態を保持する</span><span class="sxs-lookup"><span data-stu-id="310df-126">Preserve state across circuits</span></span>

<span data-ttu-id="310df-127">場合によっては、回線間で状態を維持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="310df-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="310df-128">次の場合、アプリはユーザーの重要なデータを保持できます。</span><span class="sxs-lookup"><span data-stu-id="310df-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="310df-129">Web サーバーが使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="310df-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="310df-130">ユーザーのブラウザーは、新しい web サーバーで新しい回線を開始することを強制されます。</span><span class="sxs-lookup"><span data-stu-id="310df-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="310df-131">一般に、回線をまたいで状態を維持することは、既に存在するデータを単に読み取るだけではなく、ユーザーが積極的にデータを作成するシナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="310df-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="310df-132">1つの回線を超えて状態を維持するために、*データをサーバーのメモリに格納するだけではいけません*。</span><span class="sxs-lookup"><span data-stu-id="310df-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="310df-133">アプリは、他の保存場所にデータを保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="310df-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="310df-134">状態の永続化は自動ではありません&mdash;ステートフルなデータ永続化を実装するには、アプリを開発するときに手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="310df-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="310df-135">通常、データの永続化は、ユーザーが作成に労力を費やす高価値状態の場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="310df-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="310df-136">次の例では、状態の永続化によって時間が節約されるか、商用活動の支援が行われます。</span><span class="sxs-lookup"><span data-stu-id="310df-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="310df-137">複数ステップの web フォーム &ndash;、ユーザーが状態が失われた場合に、複数の手順で完了したプロセスのデータを再入力するのに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="310df-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="310df-138">このシナリオでは、ユーザーがマルチステップ形式から移動し、後でフォームに戻ると、状態が失われます。</span><span class="sxs-lookup"><span data-stu-id="310df-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="310df-139">ショッピングカート &ndash;、潜在的な収益を表すアプリの商用の重要なコンポーネントを維持することができます。</span><span class="sxs-lookup"><span data-stu-id="310df-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="310df-140">自分の状態やショッピングカートを紛失したユーザーは、後でサイトに戻ったときに、より多くの製品やサービスを購入することができます。</span><span class="sxs-lookup"><span data-stu-id="310df-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="310df-141">通常、送信されていないサインインダイアログに入力したユーザー名など、簡単に再作成された状態を維持する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="310df-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="310df-142">アプリは、アプリの*状態*のみを保持できます。</span><span class="sxs-lookup"><span data-stu-id="310df-142">An app can only persist *app state*.</span></span> <span data-ttu-id="310df-143">コンポーネントインスタンスやそのレンダリングツリーなど、Ui を永続化することはできません。</span><span class="sxs-lookup"><span data-stu-id="310df-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="310df-144">コンポーネントとレンダリングツリーは、一般にシリアル化できません。</span><span class="sxs-lookup"><span data-stu-id="310df-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="310df-145">TreeView の展開されたノードなど、UI の状態に似たものを保持するには、アプリにカスタムコードを用意して、動作を serializable アプリの状態としてモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="310df-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="310df-146">状態を保持する場所</span><span class="sxs-lookup"><span data-stu-id="310df-146">Where to persist state</span></span>

<span data-ttu-id="310df-147">Blazor サーバーアプリで状態を永続化するには、3つの一般的な場所が存在します。</span><span class="sxs-lookup"><span data-stu-id="310df-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="310df-148">各アプローチは、さまざまなシナリオに最適であり、さまざまな注意点があります。</span><span class="sxs-lookup"><span data-stu-id="310df-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="310df-149">データベース内のサーバー側</span><span class="sxs-lookup"><span data-stu-id="310df-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="310df-150">URL</span><span class="sxs-lookup"><span data-stu-id="310df-150">URL</span></span>](#url)
* [<span data-ttu-id="310df-151">クライアント側 (ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="310df-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="310df-152">データベース内のサーバー側</span><span class="sxs-lookup"><span data-stu-id="310df-152">Server-side in a database</span></span>

<span data-ttu-id="310df-153">永続的なデータ永続化や、複数のユーザーまたはデバイスにまたがる必要があるデータについては、独立したサーバー側データベースが最適な選択肢です。</span><span class="sxs-lookup"><span data-stu-id="310df-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="310df-154">次のオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="310df-154">Options include:</span></span>

* <span data-ttu-id="310df-155">リレーショナル SQL データベース</span><span class="sxs-lookup"><span data-stu-id="310df-155">Relational SQL database</span></span>
* <span data-ttu-id="310df-156">キー/値ストア</span><span class="sxs-lookup"><span data-stu-id="310df-156">Key-value store</span></span>
* <span data-ttu-id="310df-157">Blob ストア</span><span class="sxs-lookup"><span data-stu-id="310df-157">Blob store</span></span>
* <span data-ttu-id="310df-158">テーブルストア</span><span class="sxs-lookup"><span data-stu-id="310df-158">Table store</span></span>

<span data-ttu-id="310df-159">データがデータベースに保存された後は、ユーザーがいつでも新しい回線を開始できます。</span><span class="sxs-lookup"><span data-stu-id="310df-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="310df-160">ユーザーのデータは、すべての新しい回線で保持され、使用できます。</span><span class="sxs-lookup"><span data-stu-id="310df-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="310df-161">Azure のデータストレージオプションの詳細については、 [Azure Storage のドキュメント](/azure/storage/)と[azure データベース](https://azure.microsoft.com/product-categories/databases/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="310df-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="310df-162">URL</span><span class="sxs-lookup"><span data-stu-id="310df-162">URL</span></span>

<span data-ttu-id="310df-163">ナビゲーション状態を表す一時的なデータの場合は、URL の一部としてデータをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="310df-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="310df-164">URL でモデル化された状態の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="310df-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="310df-165">表示されているエンティティの ID。</span><span class="sxs-lookup"><span data-stu-id="310df-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="310df-166">ページングされたグリッド内の現在のページ番号。</span><span class="sxs-lookup"><span data-stu-id="310df-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="310df-167">ブラウザーのアドレスバーの内容は保持されます。</span><span class="sxs-lookup"><span data-stu-id="310df-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="310df-168">ユーザーが手動でページを再読み込みした場合。</span><span class="sxs-lookup"><span data-stu-id="310df-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="310df-169">Web サーバーが使用できなくなった場合&mdash;別のサーバーに接続するために、ユーザーはページの再読み込みを強制されます。</span><span class="sxs-lookup"><span data-stu-id="310df-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="310df-170">`@page` ディレクティブを使用した URL パターンの定義については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="310df-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="310df-171">クライアント側 (ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="310df-171">Client-side in the browser</span></span>

<span data-ttu-id="310df-172">ユーザーがアクティブに作成している一時的なデータの場合、共通のバッキングストアは、ブラウザーの `localStorage` コレクションと `sessionStorage` コレクションです。</span><span class="sxs-lookup"><span data-stu-id="310df-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="310df-173">回線が破棄された場合、保存されている状態を管理またはクリアするためにアプリは必要ありません。これは、サーバー側の記憶域よりも利点があります。</span><span class="sxs-lookup"><span data-stu-id="310df-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="310df-174">このセクションの「クライアント側」では、ブラウザーでのクライアント側のシナリオを参照していますが、 [Blazor WebAssembly ホスティングモデル](xref:blazor/hosting-models#blazor-webassembly)ではありません。</span><span class="sxs-lookup"><span data-stu-id="310df-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="310df-175">`localStorage` と `sessionStorage` は、Blazor WebAssembly で使用できますが、カスタムコードを記述したり、サードパーティのパッケージを使用したりすることによってのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="310df-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="310df-176">`localStorage` と `sessionStorage` は次のように異なります。</span><span class="sxs-lookup"><span data-stu-id="310df-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="310df-177">`localStorage` は、ユーザーのブラウザーにスコープが設定されています。</span><span class="sxs-lookup"><span data-stu-id="310df-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="310df-178">ユーザーがページを再読み込みするか、ブラウザーを閉じてから再度開くと、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="310df-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="310df-179">ユーザーが複数のブラウザータブを開いた場合、状態はタブ間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="310df-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="310df-180">明示的にクリアされるまで、データは `localStorage` に保持されます。</span><span class="sxs-lookup"><span data-stu-id="310df-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="310df-181">`sessionStorage` は、ユーザーのブラウザータブにスコープが設定されています。ユーザーがタブを再読み込みすると、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="310df-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="310df-182">ユーザーがタブまたはブラウザーを閉じた場合、状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="310df-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="310df-183">ユーザーが複数のブラウザータブを開いた場合、各タブには独立したバージョンのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="310df-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="310df-184">一般に、`sessionStorage` を使用する方が安全です。</span><span class="sxs-lookup"><span data-stu-id="310df-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="310df-185">`sessionStorage` すると、ユーザーが複数のタブを開いて次のことが発生するリスクを回避できます。</span><span class="sxs-lookup"><span data-stu-id="310df-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="310df-186">タブ間の状態ストレージのバグ。</span><span class="sxs-lookup"><span data-stu-id="310df-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="310df-187">タブが他のタブの状態を上書きするときの動作がわかりにくい。</span><span class="sxs-lookup"><span data-stu-id="310df-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="310df-188">`localStorage` は、ブラウザーを閉じて再度開いている間にアプリが状態を保持する必要がある場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="310df-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="310df-189">ブラウザーの記憶域を使用する場合の注意事項:</span><span class="sxs-lookup"><span data-stu-id="310df-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="310df-190">サーバー側データベースを使用する場合と同様に、データの読み込みと保存は非同期です。</span><span class="sxs-lookup"><span data-stu-id="310df-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="310df-191">サーバー側データベースとは異なり、プリステージ中に要求されたページがブラウザーに存在しないため、プリステージ中にストレージを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="310df-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="310df-192">Blazor サーバーアプリで保持するには、数キロバイトのデータを格納するのが妥当です。</span><span class="sxs-lookup"><span data-stu-id="310df-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="310df-193">ネットワーク経由でデータが読み込まれ、保存されるため、数キロバイトを超えるパフォーマンスへの影響を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="310df-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="310df-194">ユーザーは、データの表示や改ざんを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="310df-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="310df-195">ASP.NET Core[データ保護](xref:security/data-protection/introduction)を使用すると、リスクを軽減できます。</span><span class="sxs-lookup"><span data-stu-id="310df-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="310df-196">サードパーティのブラウザーストレージソリューション</span><span class="sxs-lookup"><span data-stu-id="310df-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="310df-197">サードパーティの NuGet パッケージは、`localStorage` と `sessionStorage`を操作するための Api を提供します。</span><span class="sxs-lookup"><span data-stu-id="310df-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="310df-198">ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を透過的に使用するパッケージを選択することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="310df-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="310df-199">ASP.NET Core データ保護は、格納されているデータを暗号化し、格納されたデータを改ざんするリスクを軽減します。</span><span class="sxs-lookup"><span data-stu-id="310df-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="310df-200">JSON でシリアル化されたデータがプレーンテキストで格納されている場合、ユーザーはブラウザー開発者ツールを使用してデータを表示し、格納されているデータも変更できます。</span><span class="sxs-lookup"><span data-stu-id="310df-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="310df-201">データのセキュリティ保護は、本質的には単純であるため、常に問題になるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="310df-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="310df-202">たとえば、UI 要素に格納されている色の読み取りや変更は、ユーザーや組織にとって重要なセキュリティ上のリスクとは言えません。</span><span class="sxs-lookup"><span data-stu-id="310df-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="310df-203">*機密データ*を検査または改ざんすることをユーザーに許可しないでください。</span><span class="sxs-lookup"><span data-stu-id="310df-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="310df-204">保護されたブラウザーストレージの試験的パッケージ</span><span class="sxs-lookup"><span data-stu-id="310df-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="310df-205">`localStorage` と `sessionStorage` の[データ保護](xref:security/data-protection/introduction)を提供する NuGet パッケージの例としては、 [AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)があります。</span><span class="sxs-lookup"><span data-stu-id="310df-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="310df-206">現時点では、運用環境での使用に適していない、サポートされていない実験的パッケージは `Microsoft.AspNetCore.ProtectedBrowserStorage`。</span><span class="sxs-lookup"><span data-stu-id="310df-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="310df-207">インストール</span><span class="sxs-lookup"><span data-stu-id="310df-207">Installation</span></span>

<span data-ttu-id="310df-208">`Microsoft.AspNetCore.ProtectedBrowserStorage` パッケージをインストールするには:</span><span class="sxs-lookup"><span data-stu-id="310df-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="310df-209">Blazor Server アプリプロジェクトで、 [ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="310df-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="310df-210">最上位レベルの HTML (たとえば、既定のプロジェクトテンプレートの*Pages/_Host*ファイル) で、次の `<script>` タグを追加します。</span><span class="sxs-lookup"><span data-stu-id="310df-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="310df-211">`Startup.ConfigureServices` メソッドで `AddProtectedBrowserStorage` を呼び出して、サービスコレクションに `localStorage` および `sessionStorage` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="310df-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="310df-212">コンポーネント内のデータを保存して読み込む</span><span class="sxs-lookup"><span data-stu-id="310df-212">Save and load data within a component</span></span>

<span data-ttu-id="310df-213">ブラウザーストレージへのデータの読み込みまたは保存を必要とするコンポーネントでは、 [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)を使用して、次のいずれかのインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="310df-213">In any component that requires loading or saving data to browser storage, use [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="310df-214">選択は、使用するバッキングストアによって異なります。</span><span class="sxs-lookup"><span data-stu-id="310df-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="310df-215">次の例では、`sessionStorage` が使用されています。</span><span class="sxs-lookup"><span data-stu-id="310df-215">In the following example, `sessionStorage` is used:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="310df-216">`@using` ステートメントは、コンポーネントではなく *_Imports の razor*ファイルに配置できます。</span><span class="sxs-lookup"><span data-stu-id="310df-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="310df-217">*_Imports razor*ファイルを使用すると、アプリまたはアプリ全体で名前空間を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="310df-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="310df-218">プロジェクトテンプレートの `Counter` コンポーネントで `currentCount` の値を保持するには、`ProtectedSessionStore.SetAsync`を使用するように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="310df-218">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="310df-219">大規模でより現実的なアプリでは、個々のフィールドを格納するシナリオはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="310df-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="310df-220">アプリは、複雑な状態を含むモデルオブジェクト全体を格納する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="310df-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="310df-221">`ProtectedSessionStore` は、JSON データを自動的にシリアル化および逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="310df-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="310df-222">前のコード例では、`currentCount` データは `sessionStorage['count']` としてユーザーのブラウザーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="310df-222">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="310df-223">データはプレーンテキストで保存されるのではなく、ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を使用して保護されます。</span><span class="sxs-lookup"><span data-stu-id="310df-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="310df-224">暗号化されたデータは、ブラウザーの開発者コンソールで `sessionStorage['count']` が評価された場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="310df-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="310df-225">ユーザーが後で (まったく新しい回線上にいるかどうかを含めて) `Counter` コンポーネントに戻った場合に `currentCount` データを回復するには、`ProtectedSessionStore.GetAsync`を使用します。</span><span class="sxs-lookup"><span data-stu-id="310df-225">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="310df-226">コンポーネントのパラメーターにナビゲーション状態が含まれている場合は、`ProtectedSessionStore.GetAsync` を呼び出し、`OnInitializedAsync`ではなく `OnParametersSetAsync`に結果を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="310df-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="310df-227">`OnInitializedAsync` は、コンポーネントが最初にインスタンス化されるときに1回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="310df-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="310df-228">ユーザーが同じページで別の URL に移動しても、後で `OnInitializedAsync` が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="310df-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span>

> [!WARNING]
> <span data-ttu-id="310df-229">このセクションの例は、サーバーのプリレンダリングが有効になっていない場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="310df-229">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="310df-230">プリレンダリングが有効になっていると、次のようなエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="310df-230">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="310df-231">この時点では、JavaScript の相互運用呼び出しは発行できません。</span><span class="sxs-lookup"><span data-stu-id="310df-231">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="310df-232">これは、コンポーネントが prerendered されているためです。</span><span class="sxs-lookup"><span data-stu-id="310df-232">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="310df-233">プリレンダリングを無効にするか、またはプリコーディングを使用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="310df-233">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="310df-234">プリレンダリングで動作するコードの記述の詳細については、「[処理のプリ](#handle-prerendering)コーディング」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="310df-234">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="310df-235">読み込み状態を処理する</span><span class="sxs-lookup"><span data-stu-id="310df-235">Handle the loading state</span></span>

<span data-ttu-id="310df-236">ブラウザーストレージは非同期 (ネットワーク接続経由でアクセスされる) であるため、データが読み込まれてコンポーネントで使用できるようになるまでには、常に一定の時間があります。</span><span class="sxs-lookup"><span data-stu-id="310df-236">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="310df-237">最適な結果を得るには、読み込み中に、空白または既定のデータを表示するのではなく、読み込み状態メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="310df-237">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="310df-238">1つの方法は、データが `null` (まだ読み込み中) かどうかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="310df-238">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="310df-239">既定の `Counter` コンポーネントでは、カウントは `int`に保持されます。</span><span class="sxs-lookup"><span data-stu-id="310df-239">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="310df-240">型 (`int`) に疑問符 (`?`) を追加して、`currentCount` null 値を許容するようにします。</span><span class="sxs-lookup"><span data-stu-id="310df-240">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="310df-241">[カウントと**インクリメント**] ボタンを無条件に表示するのではなく、データが読み込まれた場合にのみこれらの要素を表示するように選択します。</span><span class="sxs-lookup"><span data-stu-id="310df-241">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="310df-242">プリレンダリングの処理</span><span class="sxs-lookup"><span data-stu-id="310df-242">Handle prerendering</span></span>

<span data-ttu-id="310df-243">プリレンダリング中:</span><span class="sxs-lookup"><span data-stu-id="310df-243">During prerendering:</span></span>

* <span data-ttu-id="310df-244">ユーザーのブラウザーへの対話型接続は存在しません。</span><span class="sxs-lookup"><span data-stu-id="310df-244">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="310df-245">ブラウザーには、JavaScript コードを実行できるページがまだありません。</span><span class="sxs-lookup"><span data-stu-id="310df-245">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="310df-246">`localStorage` または `sessionStorage` を、プリレンダリング中に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="310df-246">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="310df-247">コンポーネントがストレージを操作しようとすると、次のようなエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="310df-247">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="310df-248">この時点では、JavaScript の相互運用呼び出しは発行できません。</span><span class="sxs-lookup"><span data-stu-id="310df-248">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="310df-249">これは、コンポーネントが prerendered されているためです。</span><span class="sxs-lookup"><span data-stu-id="310df-249">This is because the component is being prerendered.</span></span>

<span data-ttu-id="310df-250">このエラーを解決する方法の1つは、プリレンダリングを無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="310df-250">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="310df-251">これは通常、アプリでブラウザーベースのストレージを多用する場合に最適な選択肢です。</span><span class="sxs-lookup"><span data-stu-id="310df-251">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="310df-252">プリレンダリングによって複雑さが増すため、アプリは `localStorage` または `sessionStorage` が利用可能になるまでは、役に立つコンテンツを事前に使用できません。</span><span class="sxs-lookup"><span data-stu-id="310df-252">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="310df-253">プリレンダリングを無効にするには、 *Pages/_Host cshtml*ファイルを開き、`Component` タグヘルパーの `render-mode` への呼び出しを `Server`に変更します。</span><span class="sxs-lookup"><span data-stu-id="310df-253">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `render-mode` of the `Component` Tag Helper to `Server`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

<span data-ttu-id="310df-254">プリレンダリングを無効にするには、 *Pages/_Host cshtml*ファイルを開き、`Html.RenderComponentAsync<App>(RenderMode.Server)`への呼び出しを変更します。</span><span class="sxs-lookup"><span data-stu-id="310df-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `Html.RenderComponentAsync<App>(RenderMode.Server)`.</span></span>

::: moniker-end

<span data-ttu-id="310df-255">`localStorage` または `sessionStorage`を使用しない他のページでは、プリレンダリングが役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="310df-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="310df-256">プリレンダリングが有効な状態を維持するには、ブラウザーが回線に接続されるまで読み込み操作を延期します。</span><span class="sxs-lookup"><span data-stu-id="310df-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="310df-257">次に、カウンター値を格納する例を示します。</span><span class="sxs-lookup"><span data-stu-id="310df-257">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="310df-258">共通の場所に保持される状態を考慮する</span><span class="sxs-lookup"><span data-stu-id="310df-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="310df-259">多くのコンポーネントがブラウザーベースのストレージに依存している場合は、状態プロバイダーコードを何度も再実装すると、コードの重複が発生します。</span><span class="sxs-lookup"><span data-stu-id="310df-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="310df-260">コードの重複を回避するためのオプションの1つは、状態プロバイダーのロジックをカプセル化する*状態プロバイダーの親コンポーネント*を作成することです。</span><span class="sxs-lookup"><span data-stu-id="310df-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="310df-261">子コンポーネントは、状態の永続化メカニズムに関係なく、永続化されたデータを処理できます。</span><span class="sxs-lookup"><span data-stu-id="310df-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="310df-262">次の `CounterStateProvider` コンポーネントの例では、カウンターデータが永続化されます。</span><span class="sxs-lookup"><span data-stu-id="310df-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

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

<span data-ttu-id="310df-263">`CounterStateProvider` コンポーネントは、読み込みが完了するまで子コンテンツをレンダリングしないことによって、読み込みフェーズを処理します。</span><span class="sxs-lookup"><span data-stu-id="310df-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="310df-264">`CounterStateProvider` コンポーネントを使用するには、カウンターの状態へのアクセスを必要とする他のコンポーネントの周囲にコンポーネントのインスタンスをラップします。</span><span class="sxs-lookup"><span data-stu-id="310df-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="310df-265">アプリ内のすべてのコンポーネントから状態にアクセスできるようにするには、`App` コンポーネント (*app.xaml*) の `Router` の周囲に `CounterStateProvider` コンポーネントをラップします。</span><span class="sxs-lookup"><span data-stu-id="310df-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="310df-266">ラップされたコンポーネントはを受け取り、永続化されたカウンターの状態を変更できます。</span><span class="sxs-lookup"><span data-stu-id="310df-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="310df-267">次の `Counter` コンポーネントは、というパターンを実装しています。</span><span class="sxs-lookup"><span data-stu-id="310df-267">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="310df-268">前のコンポーネントは、`ProtectedBrowserStorage`と対話する必要がなく、"読み込み中" フェーズにも対応していません。</span><span class="sxs-lookup"><span data-stu-id="310df-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="310df-269">前に説明したように、プリレンダリングを処理するには、カウンターデータを使用するすべてのコンポーネントがプリレンダリングを使用して自動的に動作するように `CounterStateProvider` を修正できます。</span><span class="sxs-lookup"><span data-stu-id="310df-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="310df-270">詳細については、「[ハンドルのプリレンダリング](#handle-prerendering)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="310df-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="310df-271">一般に、*状態プロバイダーの親コンポーネント*パターンをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="310df-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="310df-272">他の多くのコンポーネントで状態を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="310df-272">To consume state in many other components.</span></span>
* <span data-ttu-id="310df-273">保持する最上位レベルの状態オブジェクトが1つだけの場合。</span><span class="sxs-lookup"><span data-stu-id="310df-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="310df-274">さまざまな状態オブジェクトを保持し、異なる場所にあるオブジェクトの異なるサブセットを使用するには、状態の読み込みと保存をグローバルに処理しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="310df-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
