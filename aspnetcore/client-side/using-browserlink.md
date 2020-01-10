---
title: ASP.NET Core のブラウザーリンク
author: ncarandini
description: ブラウザーリンクが Visual Studio の機能であり、開発環境を1つ以上の web ブラウザーにリンクする方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828271"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="d7952-103">ASP.NET Core のブラウザーリンク</span><span class="sxs-lookup"><span data-stu-id="d7952-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="d7952-104">[Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)、 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d7952-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d7952-105">ブラウザーリンクは Visual Studio の機能です。</span><span class="sxs-lookup"><span data-stu-id="d7952-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="d7952-106">開発環境と1つ以上の web ブラウザーの間に通信チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d7952-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="d7952-107">ブラウザーリンクを使用して、複数のブラウザーで web アプリを一度に更新することができます。これは、ブラウザー間のテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d7952-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="d7952-108">ブラウザーリンクの設定</span><span class="sxs-lookup"><span data-stu-id="d7952-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7952-109">プロジェクトに、 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="d7952-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="d7952-110">ASP.NET Core Razor Pages または MVC プロジェクトの場合は、「<xref:mvc/views/view-compilation>」で説明されているように、Razor (*cshtml*) ファイルのランタイムコンパイルも有効にします。</span><span class="sxs-lookup"><span data-stu-id="d7952-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="d7952-111">Razor 構文の変更は、ランタイムコンパイルが有効になっている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="d7952-112">ASP.NET Core 2.0 プロジェクトを ASP.NET Core 2.1 に変換し、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に移行する場合は、ブラウザーリンク機能用の[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージをインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="d7952-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="d7952-113">ASP.NET Core 2.1 プロジェクトテンプレートでは、既定で `Microsoft.AspNetCore.App` メタパッケージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="d7952-114">ASP.NET Core 2.0 **Web アプリケーション**、**空**、および **web API** プロジェクトテンプレートでは、[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) へのパッケージ参照を含む [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage) が使用されています。</span><span class="sxs-lookup"><span data-stu-id="d7952-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="d7952-115">そのため、`Microsoft.AspNetCore.All` メタパッケージを使用する場合は、ブラウザーリンクを使用できるようにするための追加の操作は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d7952-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="d7952-116">ASP.NET Core 1.x **Web アプリケーション**プロジェクトテンプレートには、 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージのパッケージ参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d7952-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="d7952-117">その他のプロジェクトの種類では、`Microsoft.VisualStudio.Web.BrowserLink`にパッケージ参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7952-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="d7952-118">の構成</span><span class="sxs-lookup"><span data-stu-id="d7952-118">Configuration</span></span>

<span data-ttu-id="d7952-119">`Startup.Configure` メソッドで `UseBrowserLink` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d7952-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="d7952-120">`UseBrowserLink` の呼び出しは、通常、開発環境でブラウザーリンクのみを有効にする `if` ブロック内に配置されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="d7952-121">例:</span><span class="sxs-lookup"><span data-stu-id="d7952-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="d7952-122">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7952-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="d7952-123">ブラウザーリンクを使用する方法</span><span class="sxs-lookup"><span data-stu-id="d7952-123">How to use Browser Link</span></span>

<span data-ttu-id="d7952-124">ASP.NET Core のプロジェクトを開いている場合、Visual Studio では、**デバッグターゲット** ツールバーコントロールの横に ブラウザーリンク ツールバーコントロールが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![ブラウザーリンクのドロップダウンメニュー](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="d7952-126">[ブラウザーリンク] ツールバーコントロールからは、次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d7952-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="d7952-127">複数のブラウザーで web アプリを一度に更新します。</span><span class="sxs-lookup"><span data-stu-id="d7952-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="d7952-128">**ブラウザーリンクダッシュボード**を開きます。</span><span class="sxs-lookup"><span data-stu-id="d7952-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="d7952-129">**ブラウザーリンク**を有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="d7952-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="d7952-130">注: Visual Studio では、ブラウザーリンクは既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="d7952-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="d7952-131">[CSS 自動同期](#enable-or-disable-css-auto-sync)を有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="d7952-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="d7952-132">複数のブラウザーで web アプリを一度に更新する</span><span class="sxs-lookup"><span data-stu-id="d7952-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="d7952-133">プロジェクトを開始するときに起動する1つの web ブラウザーを選択するには、 **[デバッグターゲット]** ツールバーコントロールのドロップダウンメニューを使用します。</span><span class="sxs-lookup"><span data-stu-id="d7952-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 ドロップダウンメニュー](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="d7952-135">一度に複数のブラウザーを開く、次のように選択します**を参照しています...** 同じドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="d7952-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="d7952-136"><kbd>Ctrl</kbd>キーを押しながら目的のブラウザーを選択し、 **[参照]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7952-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![多数のブラウザーを一度に開く](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="d7952-138">次のスクリーンショットは、インデックスビューが開いている Visual Studio と、2つの開いているブラウザーを示しています。</span><span class="sxs-lookup"><span data-stu-id="d7952-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![2つのブラウザーを使用した同期の例](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="d7952-140">プロジェクトに接続されているブラウザーを表示するには、[ブラウザーリンク] ツールバーコントロールの上にマウスポインターを移動します。</span><span class="sxs-lookup"><span data-stu-id="d7952-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![ホバーヒント](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="d7952-142">インデックスビューを変更すると、ブラウザーのリンクの [更新] ボタンをクリックすると、接続されているすべてのブラウザーが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="d7952-144">ブラウザーリンクは、Visual Studio の外部から起動するブラウザーでも動作し、アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="d7952-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="d7952-145">ブラウザーリンクダッシュボード</span><span class="sxs-lookup"><span data-stu-id="d7952-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="d7952-146">ブラウザーリンクのドロップダウンメニューから**ブラウザーリンクダッシュボード**ウィンドウを開いて、開いているブラウザーとの接続を管理します。</span><span class="sxs-lookup"><span data-stu-id="d7952-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![browserslink-ダッシュボード](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="d7952-148">ブラウザーが接続されていない場合は、 **[ブラウザーで表示]** リンクを選択して、非デバッグセッションを開始できます。</span><span class="sxs-lookup"><span data-stu-id="d7952-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="d7952-150">それ以外の場合は、接続されているブラウザーが、各ブラウザーに表示されているページへのパスと共に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="d7952-152">個々のブラウザー名をクリックして、そのブラウザーのみを更新することもできます。</span><span class="sxs-lookup"><span data-stu-id="d7952-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="d7952-153">ブラウザーリンクを有効または無効にする</span><span class="sxs-lookup"><span data-stu-id="d7952-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="d7952-154">ブラウザーリンクを無効にした後で再度有効にする場合は、ブラウザーを更新して再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7952-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="d7952-155">CSS 自動同期を有効または無効にする</span><span class="sxs-lookup"><span data-stu-id="d7952-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="d7952-156">CSS 自動同期が有効になっていると、CSS ファイルに変更を加えたときに、接続されているブラウザーが自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="d7952-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="d7952-157">機能のしくみ</span><span class="sxs-lookup"><span data-stu-id="d7952-157">How it works</span></span>

<span data-ttu-id="d7952-158">ブラウザーリンクは[SignalR](xref:signalr/introduction)を使用して、Visual Studio とブラウザーの間の通信チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d7952-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="d7952-159">ブラウザーリンクが有効になっている場合、Visual Studio は複数のクライアント (ブラウザー) が接続できる SignalR サーバーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="d7952-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="d7952-160">ブラウザーリンクは、ASP.NET Core 要求パイプラインにミドルウェアコンポーネントも登録します。</span><span class="sxs-lookup"><span data-stu-id="d7952-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="d7952-161">このコンポーネントは、サーバーからのすべてのページ要求に特別な `<script>` 参照を挿入します。</span><span class="sxs-lookup"><span data-stu-id="d7952-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="d7952-162">スクリプト参照を表示するには、ブラウザーで **[ソースの表示]** を選択し、`<body>` タグコンテンツの最後までスクロールします。</span><span class="sxs-lookup"><span data-stu-id="d7952-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="d7952-163">ソースファイルは変更されていません。</span><span class="sxs-lookup"><span data-stu-id="d7952-163">Your source files aren't modified.</span></span> <span data-ttu-id="d7952-164">ミドルウェアコンポーネントは、スクリプト参照を動的に挿入します。</span><span class="sxs-lookup"><span data-stu-id="d7952-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="d7952-165">ブラウザー側のコードはすべて JavaScript であるため、ブラウザープラグインを必要とせずにサポート SignalR すべてのブラウザーで動作します。</span><span class="sxs-lookup"><span data-stu-id="d7952-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
