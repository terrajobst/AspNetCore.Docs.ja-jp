---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 選択したツールで Blazor アプリを構築して、Blazor を開始しましょう。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 7d495bddde3c01c743db9757204a5cf59d8b160b
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550324"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="4acd2-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="4acd2-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="4acd2-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4acd2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4acd2-105">Blazorの概要:</span><span class="sxs-lookup"><span data-stu-id="4acd2-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="4acd2-106">[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="4acd2-107">コマンドシェルで次のコマンドを実行して、 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="4acd2-108">[BlazorAspNetCore です。テンプレート](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor Webasはプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="4acd2-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. <span data-ttu-id="4acd2-109">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4acd2-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4acd2-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="4acd2-111">1 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-111">1\.</span></span> <span data-ttu-id="4acd2-112">**ASP.NET と web 開発**ワークロードを使用して、 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/)以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="4acd2-113">2 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-113">2\.</span></span> <span data-ttu-id="4acd2-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-114">Create a new project.</span></span>

   <span data-ttu-id="4acd2-115">3 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-115">3\.</span></span> <span data-ttu-id="4acd2-116">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-116">Select **Blazor App**.</span></span> <span data-ttu-id="4acd2-117">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-117">Select **Next**.</span></span>

   <span data-ttu-id="4acd2-118">4 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-118">4\.</span></span> <span data-ttu-id="4acd2-119">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="4acd2-120">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4acd2-121">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-121">Select **Create**.</span></span>

   <span data-ttu-id="4acd2-122">5 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-122">5\.</span></span> <span data-ttu-id="4acd2-123">Blazor webassembly については、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4acd2-124">Blazor サーバーのエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="4acd2-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-125">Select **Create**.</span></span> <span data-ttu-id="4acd2-126">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="4acd2-127">6 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-127">6\.</span></span> <span data-ttu-id="4acd2-128">**Ctrl** + **F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="4acd2-129">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="4acd2-130">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="4acd2-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4acd2-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4acd2-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="4acd2-132">1 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-132">1\.</span></span> <span data-ttu-id="4acd2-133">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="4acd2-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="4acd2-134">2 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-134">2\.</span></span> <span data-ttu-id="4acd2-135">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="4acd2-136">3 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-136">3\.</span></span> <span data-ttu-id="4acd2-137">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="4acd2-138">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="4acd2-139">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="4acd2-140">4 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-140">4\.</span></span> <span data-ttu-id="4acd2-141">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="4acd2-142">5 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-142">5\.</span></span> <span data-ttu-id="4acd2-143">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="4acd2-144">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-144">Select **Yes**.</span></span>

   <span data-ttu-id="4acd2-145">6 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-145">6\.</span></span> <span data-ttu-id="4acd2-146">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="4acd2-147">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="4acd2-148">7 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-148">7\.</span></span> <span data-ttu-id="4acd2-149">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4acd2-150">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4acd2-150">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="4acd2-151">1 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-151">1\.</span></span> <span data-ttu-id="4acd2-152">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-152">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="4acd2-153">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-153">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="4acd2-154">2 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-154">2\.</span></span> <span data-ttu-id="4acd2-155">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-155">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="4acd2-156">3 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-156">3\.</span></span> <span data-ttu-id="4acd2-157">サイドバーで、[ **.Net Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-157">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="4acd2-158">4 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-158">4\.</span></span> <span data-ttu-id="4acd2-159">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-159">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="4acd2-160">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="4acd2-160">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="4acd2-161">Blazor Webasのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-161">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="4acd2-162">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-162">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="4acd2-163">5 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-163">5\.</span></span> <span data-ttu-id="4acd2-164">**ターゲットフレームワーク**は、既定で **.net core 3.0** (または、3.1 Preview SDK がインストールされている場合は **.net core 3.1** ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-164">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="4acd2-165">フレームワークを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-165">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="4acd2-166">6 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-166">6\.</span></span> <span data-ttu-id="4acd2-167">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="4acd2-168">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-168">Select **Create**.</span></span>

   <span data-ttu-id="4acd2-169">7 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-169">7\.</span></span> <span data-ttu-id="4acd2-170">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="4acd2-171">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4acd2-172">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4acd2-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="4acd2-173">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-173">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="4acd2-174">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-174">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="4acd2-175">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-175">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="4acd2-176">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-176">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="4acd2-177">最新の[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)リリースをインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-177">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="4acd2-178">必要に応じて、 [.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールし、コマンドシェルで次のコマンドを実行して、 [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly)テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-178">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. <span data-ttu-id="4acd2-179">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-179">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4acd2-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4acd2-180">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="4acd2-181">1 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-181">1\.</span></span> <span data-ttu-id="4acd2-182">**ASP.NET と web 開発**ワークロードを使用して、最新の[Visual Studio](https://visualstudio.com/vs/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-182">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="4acd2-183">2 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-183">2\.</span></span> <span data-ttu-id="4acd2-184">必要に応じて、アプリ開発 Blazor のための**ASP.NET および web 開発**ワークロードと共に[Visual Studio 16.4 Preview 2 以降](https://visualstudio.microsoft.com/vs/preview/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-184">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="4acd2-185">3 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-185">3\.</span></span> <span data-ttu-id="4acd2-186">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-186">Create a new project.</span></span>

   <span data-ttu-id="4acd2-187">4 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-187">4\.</span></span> <span data-ttu-id="4acd2-188">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-188">Select **Blazor App**.</span></span> <span data-ttu-id="4acd2-189">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-189">Select **Next**.</span></span>

   <span data-ttu-id="4acd2-190">5 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-190">5\.</span></span> <span data-ttu-id="4acd2-191">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-191">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="4acd2-192">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-192">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4acd2-193">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-193">Select **Create**.</span></span>

   <span data-ttu-id="4acd2-194">6 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-194">6\.</span></span> <span data-ttu-id="4acd2-195">Blazor webassembly については、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-195">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4acd2-196">Blazor サーバーのエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-196">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="4acd2-197">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-197">Select **Create**.</span></span> <span data-ttu-id="4acd2-198">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-198">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="4acd2-199">7 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-199">7\.</span></span> <span data-ttu-id="4acd2-200">**F5 キー**を押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-200">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="4acd2-201">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-201">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="4acd2-202">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="4acd2-202">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4acd2-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4acd2-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="4acd2-204">1 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-204">1\.</span></span> <span data-ttu-id="4acd2-205">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="4acd2-205">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="4acd2-206">2 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-206">2\.</span></span> <span data-ttu-id="4acd2-207">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-207">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="4acd2-208">3 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-208">3\.</span></span> <span data-ttu-id="4acd2-209">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-209">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="4acd2-210">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-210">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="4acd2-211">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-211">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="4acd2-212">4 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-212">4\.</span></span> <span data-ttu-id="4acd2-213">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-213">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="4acd2-214">5 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-214">5\.</span></span> <span data-ttu-id="4acd2-215">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-215">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="4acd2-216">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-216">Select **Yes**.</span></span>

   <span data-ttu-id="4acd2-217">6 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-217">6\.</span></span> <span data-ttu-id="4acd2-218">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-218">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="4acd2-219">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-219">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="4acd2-220">7 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-220">7\.</span></span> <span data-ttu-id="4acd2-221">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-221">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4acd2-222">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4acd2-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="4acd2-223">1 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-223">1\.</span></span> <span data-ttu-id="4acd2-224">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-224">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="4acd2-225">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-225">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="4acd2-226">2 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-226">2\.</span></span> <span data-ttu-id="4acd2-227">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-227">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="4acd2-228">3 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-228">3\.</span></span> <span data-ttu-id="4acd2-229">サイドバーで、[ **.Net Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-229">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="4acd2-230">4 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-230">4\.</span></span> <span data-ttu-id="4acd2-231">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-231">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="4acd2-232">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="4acd2-232">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="4acd2-233">Blazor Webasのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-233">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="4acd2-234">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-234">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="4acd2-235">5 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-235">5\.</span></span> <span data-ttu-id="4acd2-236">**ターゲットフレームワーク**は、既定で **.net core 3.0** (または、3.1 Preview SDK がインストールされている場合は **.net core 3.1** ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-236">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="4acd2-237">フレームワークを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-237">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="4acd2-238">6 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-238">6\.</span></span> <span data-ttu-id="4acd2-239">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-239">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="4acd2-240">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-240">Select **Create**.</span></span>

   <span data-ttu-id="4acd2-241">7 \。</span><span class="sxs-lookup"><span data-stu-id="4acd2-241">7\.</span></span> <span data-ttu-id="4acd2-242">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-242">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="4acd2-243">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-243">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4acd2-244">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4acd2-244">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="4acd2-245">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-245">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="4acd2-246">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-246">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="4acd2-247">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4acd2-247">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="4acd2-248">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-248">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="4acd2-249">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-249">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="4acd2-250">のホーム</span><span class="sxs-lookup"><span data-stu-id="4acd2-250">Home</span></span>
* <span data-ttu-id="4acd2-251">カウンター</span><span class="sxs-lookup"><span data-stu-id="4acd2-251">Counter</span></span>
* <span data-ttu-id="4acd2-252">データのフェッチ</span><span class="sxs-lookup"><span data-stu-id="4acd2-252">Fetch data</span></span>

<span data-ttu-id="4acd2-253">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-253">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="4acd2-254">通常、web ページのカウンターを増やすには JavaScript を記述する必要がありC#ますが、Blazor 使用できます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-254">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="4acd2-255">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="4acd2-255">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="4acd2-256">上部の `@page` ディレクティブで指定されているように、ブラウザーでの `/counter` の要求によって、`Counter` コンポーネントがそのコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="4acd2-256">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="4acd2-257">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-257">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="4acd2-258">**[クリックし**てください] ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="4acd2-258">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="4acd2-259">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-259">The `onclick` event is fired.</span></span>
* <span data-ttu-id="4acd2-260">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-260">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="4acd2-261">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-261">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="4acd2-262">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-262">The component is rendered again.</span></span>

<span data-ttu-id="4acd2-263">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-263">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="4acd2-264">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-264">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="4acd2-265">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加して、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-265">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="4acd2-266">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="4acd2-266">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="4acd2-267">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-267">Run the app.</span></span> <span data-ttu-id="4acd2-268">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="4acd2-268">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="4acd2-269">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-269">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="4acd2-270">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-270">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="4acd2-271">`[Parameter]` 属性を持つ `IncrementAmount` のパブリックプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-271">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="4acd2-272">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-272">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="4acd2-273">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="4acd2-273">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="4acd2-274">属性を使用して、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-274">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="4acd2-275">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="4acd2-275">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="4acd2-276">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4acd2-276">Run the app.</span></span> <span data-ttu-id="4acd2-277">`Index` コンポーネントには、 **[クリックし**てください] ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="4acd2-277">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="4acd2-278">`/counter` の `Counter` コンポーネント (*Counter*) は、1つずつ増加し続けます。</span><span class="sxs-lookup"><span data-stu-id="4acd2-278">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4acd2-279">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="4acd2-279">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="4acd2-280">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4acd2-280">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
