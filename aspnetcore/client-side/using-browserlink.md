---
title: ASP.NET Core のブラウザーリンク
author: ncarandini
description: ブラウザーリンクが Visual Studio の機能であり、開発環境を1つ以上の web ブラウザーにリンクする方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962781"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="7dd06-103">ASP.NET Core のブラウザーリンク</span><span class="sxs-lookup"><span data-stu-id="7dd06-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="7dd06-104">[Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)、 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7dd06-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="7dd06-105">Browser Link は、開発環境と1つ以上の web ブラウザーの間の通信チャネルを作成する Visual Studio の機能です。</span><span class="sxs-lookup"><span data-stu-id="7dd06-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="7dd06-106">ブラウザーリンクを使用すると、複数のブラウザーで一度に web アプリケーションを更新できます。これは、ブラウザー間のテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="7dd06-107">ブラウザーリンクの設定</span><span class="sxs-lookup"><span data-stu-id="7dd06-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7dd06-108">ASP.NET Core 2.0 プロジェクトを ASP.NET Core 2.1 に変換し、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に移行する場合は、browserlink 機能用の[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7dd06-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="7dd06-109">ASP.NET Core 2.1 プロジェクトテンプレートでは、既定で `Microsoft.AspNetCore.App` メタパッケージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7dd06-110">ASP.NET Core 2.0 **Web アプリケーション**、**空**、および**web API**プロジェクトテンプレートでは、メタパッケージのパッケージ参照を含む[AspNetCore](xref:fundamentals/metapackage)が使用されて[いますが](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)、</span><span class="sxs-lookup"><span data-stu-id="7dd06-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="7dd06-111">そのため、`Microsoft.AspNetCore.All` メタパッケージを使用するには、ブラウザーリンクを使用できるようにするための追加の操作は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7dd06-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="7dd06-112">ASP.NET Core 1.x **Web アプリケーション**プロジェクトテンプレートには、 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージのパッケージ参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7dd06-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="7dd06-113">**空**のプロジェクトまたは**Web API**テンプレートプロジェクトでは、`Microsoft.VisualStudio.Web.BrowserLink`にパッケージ参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7dd06-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="7dd06-114">これは Visual Studio の機能であるため、**空**のまたは**Web API**テンプレートプロジェクトにパッケージを追加する最も簡単な方法は、**パッケージマネージャーコンソール**(**他の Windows** >**パッケージマネージャーコンソール**を**表示**>) を開き、次のコマンドを実行することです。</span><span class="sxs-lookup"><span data-stu-id="7dd06-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="7dd06-115">または、 **NuGet パッケージマネージャー**を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="7dd06-116">**ソリューションエクスプローラー**でプロジェクト名を右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![NuGet パッケージマネージャーを開く](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="7dd06-118">パッケージを検索してインストールします。</span><span class="sxs-lookup"><span data-stu-id="7dd06-118">Find and install the package:</span></span>

![NuGet パッケージマネージャーを使用したパッケージの追加](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="7dd06-120">構成</span><span class="sxs-lookup"><span data-stu-id="7dd06-120">Configuration</span></span>

<span data-ttu-id="7dd06-121">`Startup.Configure` メソッドの場合:</span><span class="sxs-lookup"><span data-stu-id="7dd06-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="7dd06-122">通常、コードは、次に示すように、開発環境で Browser リンクを有効にする `if` ブロック内にあります。</span><span class="sxs-lookup"><span data-stu-id="7dd06-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="7dd06-123">詳細については、「[Use multiple environments](xref:fundamentals/environments)」(複数の環境の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7dd06-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="7dd06-124">ブラウザーリンクを使用する方法</span><span class="sxs-lookup"><span data-stu-id="7dd06-124">How to use Browser Link</span></span>

<span data-ttu-id="7dd06-125">ASP.NET Core のプロジェクトを開いている場合、Visual Studio では、**デバッグターゲット** ツールバーコントロールの横に ブラウザーリンク ツールバーコントロールが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![ブラウザーリンクのドロップダウンメニュー](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="7dd06-127">[ブラウザーリンク] ツールバーコントロールからは、次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="7dd06-128">複数のブラウザーで web アプリケーションを一度に更新します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="7dd06-129">**ブラウザーリンクダッシュボード**を開きます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="7dd06-130">**ブラウザーリンク**を有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="7dd06-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="7dd06-131">注: Visual Studio 2017 (15.3) では、ブラウザーリンクは既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="7dd06-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="7dd06-132">[CSS 自動同期](#enable-or-disable-css-auto-sync)を有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="7dd06-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="7dd06-133">一部の Visual Studio プラグイン (特に*Web Extension pack 2015*および*web extension pack 2017*) は、ブラウザーリンク用の拡張機能を提供しますが、ASP.NET Core プロジェクトでは機能しません。</span><span class="sxs-lookup"><span data-stu-id="7dd06-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="7dd06-134">複数のブラウザーで web アプリを一度に更新する</span><span class="sxs-lookup"><span data-stu-id="7dd06-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="7dd06-135">プロジェクトを開始するときに起動する1つの web ブラウザーを選択するには、 **[デバッグターゲット]** ツールバーコントロールのドロップダウンメニューを使用します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 ドロップダウンメニュー](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="7dd06-137">複数のブラウザーを一度に開くには、同じドロップダウンから **[参照]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="7dd06-138">CTRL キーを押しながら目的のブラウザーを選択し、 **[参照]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7dd06-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![多数のブラウザーを一度に開く](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="7dd06-140">インデックスビューが開いている Visual Studio と2つの開いているブラウザーを示すスクリーンショットを次に示します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![2つのブラウザーを使用した同期の例](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="7dd06-142">プロジェクトに接続されているブラウザーを表示するには、[ブラウザーリンク] ツールバーコントロールの上にマウスポインターを移動します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![ホバーヒント](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="7dd06-144">インデックスビューを変更すると、ブラウザーのリンクの [更新] ボタンをクリックすると、接続されているすべてのブラウザーが更新されます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![ブラウザー-変更の同期](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="7dd06-146">ブラウザーリンクは、Visual Studio の外部から起動するブラウザーでも動作し、アプリケーションの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="7dd06-147">ブラウザーリンクダッシュボード</span><span class="sxs-lookup"><span data-stu-id="7dd06-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="7dd06-148">ブラウザーリンクのドロップダウンメニューからブラウザーリンクダッシュボードを開いて、開いているブラウザーとの接続を管理します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![browserslink-ダッシュボード](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="7dd06-150">ブラウザーが接続されていない場合は、[*ブラウザーで表示*] リンクを選択して、非デバッグセッションを開始できます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-ダッシュボード-接続なし](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="7dd06-152">それ以外の場合は、接続されているブラウザーが、各ブラウザーに表示されているページへのパスと共に表示されます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-2-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="7dd06-154">必要に応じて、表示されているブラウザー名をクリックして、その1つのブラウザーを更新できます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="7dd06-155">ブラウザーリンクを有効または無効にする</span><span class="sxs-lookup"><span data-stu-id="7dd06-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="7dd06-156">ブラウザーリンクを無効にした後で再度有効にする場合は、ブラウザーを更新して再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7dd06-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="7dd06-157">CSS 自動同期を有効または無効にする</span><span class="sxs-lookup"><span data-stu-id="7dd06-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="7dd06-158">CSS 自動同期が有効になっていると、CSS ファイルに変更を加えたときに、接続されているブラウザーが自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="7dd06-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="7dd06-159">しくみ</span><span class="sxs-lookup"><span data-stu-id="7dd06-159">How it works</span></span>

<span data-ttu-id="7dd06-160">ブラウザーリンクは SignalR を使用して、Visual Studio とブラウザーの間の通信チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="7dd06-161">ブラウザーリンクが有効になっている場合、Visual Studio は複数のクライアント (ブラウザー) が接続できる SignalR サーバーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="7dd06-162">ブラウザーリンクは、ASP.NET Core 要求パイプラインにミドルウェアコンポーネントも登録します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="7dd06-163">このコンポーネントは、サーバーからのすべてのページ要求に特別な `<script>` 参照を挿入します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="7dd06-164">スクリプト参照を表示するには、ブラウザーで **[ソースの表示]** を選択し、`<body>` タグコンテンツの最後までスクロールします。</span><span class="sxs-lookup"><span data-stu-id="7dd06-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="7dd06-165">ソースファイルは変更されていません。</span><span class="sxs-lookup"><span data-stu-id="7dd06-165">Your source files aren't modified.</span></span> <span data-ttu-id="7dd06-166">ミドルウェアコンポーネントは、スクリプト参照を動的に挿入します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="7dd06-167">ブラウザー側のコードはすべて JavaScript であるため、ブラウザープラグインを必要とせずにサポート SignalR すべてのブラウザーで動作します。</span><span class="sxs-lookup"><span data-stu-id="7dd06-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
