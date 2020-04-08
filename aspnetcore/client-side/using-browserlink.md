---
title: ASP.NET Core の Browser Link
author: ncarandini
description: Browser Link が Visual Studio の機能であり、これにより開発環境を 1 つまたは複数の Web ブラウザーにリンクする方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647102"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="64e65-103">ASP.NET Core の Browser Link</span><span class="sxs-lookup"><span data-stu-id="64e65-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="64e65-104">作成者: [Nicolò Carandini](https://github.com/ncarandini)、[Mike Wasson](https://github.com/MikeWasson)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="64e65-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="64e65-105">Browser Link は Visual Studio の機能です。</span><span class="sxs-lookup"><span data-stu-id="64e65-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="64e65-106">これにより、開発環境と 1 つまたは複数の Web ブラウザー間に通信チャネルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="64e65-107">Browser Link を使用して、複数のブラウザーで Web アプリを一度に更新することができます。これは、ブラウザー間のテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="64e65-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="64e65-108">Browser Link のセットアップ</span><span class="sxs-lookup"><span data-stu-id="64e65-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64e65-109">[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) パッケージを自分のプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="64e65-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="64e65-110">ASP.NET Core Razor Pages または MVC プロジェクトでは、「<xref:mvc/views/view-compilation>」で説明されているように、Razor ( *.cshtml*) ファイルのランタイム コンパイルを有効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="64e65-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="64e65-111">Razor 構文の変更は、ランタイム コンパイルが有効になっている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="64e65-112">ASP.NET Core 2.0 プロジェクトを ASP.NET Core 2.1 に変換し、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に切り替える場合は、Browser Link 機能のために [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="64e65-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="64e65-113">ASP.NET Core 2.1 プロジェクト テンプレートでは、既定で `Microsoft.AspNetCore.App` メタパッケージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="64e65-114">ASP.NET Core 2.0 の **Web アプリケーション**、**空**、**Web API** プロジェクト テンプレートでは、[Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)が使用されます。これには [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 用のパッケージ参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="64e65-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="64e65-115">そのため、`Microsoft.AspNetCore.All` メタパッケージを使用する場合、Browser Link を使用できるようにするために、追加の操作を行う必要ありません。</span><span class="sxs-lookup"><span data-stu-id="64e65-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="64e65-116">ASP.NET Core 1.x の **Web アプリケーション** プロジェクト テンプレートには、[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) パッケージ用のパッケージ参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="64e65-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="64e65-117">その他のプロジェクトの種類では、`Microsoft.VisualStudio.Web.BrowserLink` にパッケージ参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="64e65-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="64e65-118">構成</span><span class="sxs-lookup"><span data-stu-id="64e65-118">Configuration</span></span>

<span data-ttu-id="64e65-119">`Startup.Configure` メソッドで `UseBrowserLink` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="64e65-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="64e65-120">`UseBrowserLink` の呼び出しは、通常、開発環境で Browser Link のみを有効にする `if` ブロック内に配置されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="64e65-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="64e65-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="64e65-122">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="64e65-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="64e65-123">Browser Link の使用方法</span><span class="sxs-lookup"><span data-stu-id="64e65-123">How to use Browser Link</span></span>

<span data-ttu-id="64e65-124">ASP.NET Core プロジェクトが開いている場合、Visual Studio では、 **[デバッグ ターゲット]** ツール バー コントロールの横に Browser Link ツール バー コントロールが表示されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Browser Link のドロップダウン メニュー](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="64e65-126">Browser Link のツール バー コントロールからは、次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="64e65-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="64e65-127">複数のブラウザーで Web アプリを一度に更新する。</span><span class="sxs-lookup"><span data-stu-id="64e65-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="64e65-128">**[Browser link ダッシュボード]** を開く。</span><span class="sxs-lookup"><span data-stu-id="64e65-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="64e65-129">**Browser Link** を有効/無効にする。</span><span class="sxs-lookup"><span data-stu-id="64e65-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="64e65-130">メモ:Visual Studio では、Browser Link は既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="64e65-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="64e65-131">[[CSS 自動同期]](#enable-or-disable-css-auto-sync) を有効/無効にする。</span><span class="sxs-lookup"><span data-stu-id="64e65-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="64e65-132">複数のブラウザーで Web アプリを一度に更新する</span><span class="sxs-lookup"><span data-stu-id="64e65-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="64e65-133">プロジェクトを開始するときに起動する 1 つの Web ブラウザーを選択するには、 **[デバッグ ターゲット]** ツール バー コントロールのドロップダウン メニューを使用します。</span><span class="sxs-lookup"><span data-stu-id="64e65-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 ドロップダウン メニュー](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="64e65-135">複数のブラウザーを一度に開くには、同じドロップダウンから **[ブラウザーの選択]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="64e65-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="64e65-136"><kbd>Ctrl</kbd> キーを押しながら目的のブラウザーを選択し、 **[参照]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="64e65-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![多数のブラウザーを一度に開く](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="64e65-138">次のスクリーンショットでは、インデックス ビューが開いている Visual Studio と、2 つの開いているブラウザーが示されています。</span><span class="sxs-lookup"><span data-stu-id="64e65-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![2 つのブラウザーとの同期の例](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="64e65-140">プロジェクトに接続されているブラウザーを表示するには、Browser Link ツール バー コントロールの上にマウス ポインターを置きます。</span><span class="sxs-lookup"><span data-stu-id="64e65-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![ポイント時のヒント](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="64e65-142">インデックス ビューを変更して、Browser Link の [更新] ボタンをクリックすると、接続されているすべてのブラウザーが更新されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="64e65-144">Browser Link は、Visual Studio 外から起動するブラウザーでも動作し、アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="64e65-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="64e65-145">Browser link ダッシュボード</span><span class="sxs-lookup"><span data-stu-id="64e65-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="64e65-146">Browser Link のドロップダウン メニューから **[Browser link ダッシュボード]** ウィンドウを開いて、開いているブラウザーとの接続を管理します。</span><span class="sxs-lookup"><span data-stu-id="64e65-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="64e65-148">ブラウザーが接続されていない場合は、 **[ブラウザーで表示]** リンクを選択することで、デバッグ以外のセッションを開始することができます。</span><span class="sxs-lookup"><span data-stu-id="64e65-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="64e65-150">それ以外の場合は、接続されているブラウザーが、各ブラウザーに表示されているページへのパスと共に表示されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="64e65-152">個々のブラウザー名をクリックして、そのブラウザーのみを更新することもできます。</span><span class="sxs-lookup"><span data-stu-id="64e65-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="64e65-153">Browser Link を有効/無効にする</span><span class="sxs-lookup"><span data-stu-id="64e65-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="64e65-154">Browser Link を無効にした後で再度有効にする場合は、ブラウザーを更新して再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="64e65-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="64e65-155">CSS 自動同期を有効/無効にする</span><span class="sxs-lookup"><span data-stu-id="64e65-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="64e65-156">CSS 自動同期が有効になっている場合、CSS ファイルに変更を加えたときに、接続されているブラウザーが自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="64e65-157">しくみ</span><span class="sxs-lookup"><span data-stu-id="64e65-157">How it works</span></span>

<span data-ttu-id="64e65-158">Browser Link では、[SignalR](xref:signalr/introduction) を使用して、Visual Studio とブラウザー間の通信チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="64e65-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="64e65-159">Browser Link が有効になっている場合、Visual Studio は複数のクライアント (ブラウザー) が接続できる SignalR サーバーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="64e65-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="64e65-160">Browser Link では、ASP.NET Core 要求パイプラインにミドルウェア コンポーネントも登録されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="64e65-161">このコンポーネントにより、サーバーからすべてのページ要求に特別な `<script>` 参照が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="64e65-162">スクリプト参照を表示するには、ブラウザーで **[ソースの表示]** を選択し、`<body>` タグ コンテンツの最後までスクロールします。</span><span class="sxs-lookup"><span data-stu-id="64e65-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="64e65-163">お使いのソース ファイルは変更されません。</span><span class="sxs-lookup"><span data-stu-id="64e65-163">Your source files aren't modified.</span></span> <span data-ttu-id="64e65-164">ミドルウェア コンポーネントによって、スクリプト参照が動的に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="64e65-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="64e65-165">ブラウザー側のコードはすべて JavaScript であるため、ブラウザー プラグインを必要とせずに、SignalR でサポートされるすべてのブラウザーで動作します。</span><span class="sxs-lookup"><span data-stu-id="64e65-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
