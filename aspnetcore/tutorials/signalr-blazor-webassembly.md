---
title: Blazor WebAssembly で ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 605cf8ebd3e85586f3e479c815f0b9902ce5a91a
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083389"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="a4717-103">Blazor WebAssembly で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="a4717-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="a4717-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4717-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a4717-105">このチュートリアルでは、Blazor WebAssembly で SignalR を使用してリアルタイム アプリを構築するための基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4717-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="a4717-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4717-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a4717-107">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="a4717-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="a4717-108">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="a4717-109">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="a4717-110">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="a4717-111">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-111">Add Razor component code for chat</span></span>

<span data-ttu-id="a4717-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="a4717-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="a4717-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a4717-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4717-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a4717-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4717-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4717-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4717-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4717-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4717-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4717-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="a4717-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4717-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="a4717-119">ホストされる Blazor WebAssembly アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="a4717-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="a4717-120">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) テンプレートをインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="a4717-120">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="a4717-121">Blazor WebAssembly がプレビュー段階にある間、[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) パッケージにはプレビュー バージョンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a4717-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="a4717-122">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
```

<span data-ttu-id="a4717-123">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a4717-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4717-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4717-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a4717-125">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4717-125">Create a new project.</span></span>

1. <span data-ttu-id="a4717-126">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="a4717-127">**[プロジェクト名]** フィールドに「BlazorSignalRApp」と入力します。</span><span class="sxs-lookup"><span data-stu-id="a4717-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="a4717-128">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="a4717-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a4717-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-129">Select **Create**.</span></span>

1. <span data-ttu-id="a4717-130">**[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="a4717-131">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a4717-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="a4717-132">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="a4717-133">新しいバージョンの Visual Studio にアップグレードした場合、またはインストールした場合で、VS の UI に Blazor WebAssembly テンプレートが表示されないときは、前述の `dotnet new` コマンドを使用してテンプレートを再インストールしてください。</span><span class="sxs-lookup"><span data-stu-id="a4717-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4717-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4717-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a4717-135">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="a4717-136">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="a4717-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="a4717-137">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="a4717-138">Visual Studio Code によって、生成された *launch.json* および *tasks.json* ファイルを含む *.vscode* フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a4717-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4717-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4717-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a4717-140">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="a4717-141">Visual Studio for Mac で、プロジェクト フォルダーに移動してプロジェクトのソリューション ファイル ( *.sln*) を開くことで、プロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="a4717-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4717-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4717-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a4717-143">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="a4717-144">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4717-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4717-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="a4717-146">**ソリューション エクスプローラー**で、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="a4717-147">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a4717-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="a4717-148">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="a4717-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="a4717-149">検索結果から `Microsoft.AspNetCore.SignalR.Client` パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="a4717-150">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="a4717-151">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4717-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4717-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="a4717-153">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4717-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4717-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a4717-155">**[ソリューション]** サイド バーで、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="a4717-156">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a4717-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="a4717-157">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="a4717-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="a4717-158">検索結果の `Microsoft.AspNetCore.SignalR.Client` パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="a4717-159">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4717-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4717-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a4717-161">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="a4717-162">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-162">Add a SignalR hub</span></span>

<span data-ttu-id="a4717-163">**BlazorSignalRApp.Server** プロジェクトで、*Hubs* (複数形) フォルダーを作成し、次の `ChatHub` クラスを追加します (*Hubs/ChatHub.cs*)。</span><span class="sxs-lookup"><span data-stu-id="a4717-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="a4717-164">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="a4717-165">**BlazorSignalRApp.Server** プロジェクトで、*Startup.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="a4717-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="a4717-166">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="a4717-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="a4717-167">`Startup.ConfigureServices` に SignalR サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4717-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="a4717-168">`Startup.Configure` で、既定のコントローラー ルートのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4717-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="a4717-169">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="a4717-170">**BlazorSignalRApp.Client** プロジェクトで、*Pages/Index.razor* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="a4717-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="a4717-171">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a4717-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="a4717-172">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="a4717-172">Run the app</span></span>

1. <span data-ttu-id="a4717-173">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a4717-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4717-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4717-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a4717-175">**ソリューション エクスプローラー**で、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="a4717-176">**Ctrl + F5** キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="a4717-177">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="a4717-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4717-178">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="a4717-179">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4717-179">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4717-181">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4717-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4717-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4717-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a4717-183">ツール バーで **[デバッグ]**  >  **[デバッグなしで実行]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="a4717-184">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="a4717-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4717-185">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="a4717-186">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4717-186">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4717-188">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4717-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4717-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4717-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a4717-190">**[ソリューション]** サイド バーで、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="a4717-191">メニューから、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="a4717-192">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="a4717-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4717-193">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="a4717-194">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4717-194">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4717-196">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4717-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4717-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4717-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="a4717-198">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4717-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="a4717-199">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="a4717-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="a4717-200">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4717-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="a4717-201">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4717-201">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="a4717-203">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="a4717-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="a4717-204">次の手順</span><span class="sxs-lookup"><span data-stu-id="a4717-204">Next steps</span></span>

<span data-ttu-id="a4717-205">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="a4717-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a4717-206">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="a4717-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="a4717-207">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="a4717-208">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="a4717-209">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="a4717-210">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="a4717-210">Add Razor component code for chat</span></span>

<span data-ttu-id="a4717-211">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4717-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="a4717-212">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a4717-212">Additional resources</span></span>

* <xref:signalr/introduction>
