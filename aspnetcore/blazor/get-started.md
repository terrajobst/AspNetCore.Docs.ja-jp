---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 選択したツールで Blazor アプリを構築して、Blazor を開始しましょう。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943766"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="f1832-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="f1832-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="f1832-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f1832-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f1832-105">Blazorの概要:</span><span class="sxs-lookup"><span data-stu-id="f1832-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="f1832-106">[.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f1832-107">必要に応じて[Blazor WebAssembly テンプレート](xref:blazor/hosting-models#blazor-webassembly)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="f1832-108">[.NET Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="f1832-109">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-109">Run the following command in a command shell.</span></span> <span data-ttu-id="f1832-110">[Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor WebAssembly はプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="f1832-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="f1832-111">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f1832-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1832-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1832-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="f1832-113">1\.</span><span class="sxs-lookup"><span data-stu-id="f1832-113">1\.</span></span> <span data-ttu-id="f1832-114">**ASP.NET と Web 開発**ワークロードを使用して、 [Visual Studio 16.4](https://visualstudio.microsoft.com/vs/preview/)以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f1832-115">2\.</span><span class="sxs-lookup"><span data-stu-id="f1832-115">2\.</span></span> <span data-ttu-id="f1832-116">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1832-116">Create a new project.</span></span>

   <span data-ttu-id="f1832-117">3\.</span><span class="sxs-lookup"><span data-stu-id="f1832-117">3\.</span></span> <span data-ttu-id="f1832-118">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-118">Select **Blazor App**.</span></span> <span data-ttu-id="f1832-119">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-119">Select **Next**.</span></span>

   <span data-ttu-id="f1832-120">4\.</span><span class="sxs-lookup"><span data-stu-id="f1832-120">4\.</span></span> <span data-ttu-id="f1832-121">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="f1832-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f1832-122">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1832-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f1832-123">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-123">Select **Create**.</span></span>

   <span data-ttu-id="f1832-124">5\.</span><span class="sxs-lookup"><span data-stu-id="f1832-124">5\.</span></span> <span data-ttu-id="f1832-125">Blazor WebAssembly については、 **Blazor WebAssembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f1832-126">Blazor サーバーのエクスペリエンスについては、 **Blazor サーバー アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f1832-127">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-127">Select **Create**.</span></span> <span data-ttu-id="f1832-128">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="f1832-129">6\.</span><span class="sxs-lookup"><span data-stu-id="f1832-129">6\.</span></span> <span data-ttu-id="f1832-130">**Ctrl** + **F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f1832-131">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="f1832-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="f1832-132">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="f1832-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1832-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1832-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="f1832-134">1\.</span><span class="sxs-lookup"><span data-stu-id="f1832-134">1\.</span></span> <span data-ttu-id="f1832-135">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="f1832-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="f1832-136">2\.</span><span class="sxs-lookup"><span data-stu-id="f1832-136">2\.</span></span> <span data-ttu-id="f1832-137">[C# Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="f1832-138">3\.</span><span class="sxs-lookup"><span data-stu-id="f1832-138">3\.</span></span> <span data-ttu-id="f1832-139">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="f1832-140">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="f1832-141">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="f1832-142">4\.</span><span class="sxs-lookup"><span data-stu-id="f1832-142">4\.</span></span> <span data-ttu-id="f1832-143">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f1832-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="f1832-144">5\.</span><span class="sxs-lookup"><span data-stu-id="f1832-144">5\.</span></span> <span data-ttu-id="f1832-145">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="f1832-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f1832-146">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-146">Select **Yes**.</span></span>

   <span data-ttu-id="f1832-147">6\.</span><span class="sxs-lookup"><span data-stu-id="f1832-147">6\.</span></span> <span data-ttu-id="f1832-148">Blazor サーバー アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="f1832-149">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="f1832-150">7 \。</span><span class="sxs-lookup"><span data-stu-id="f1832-150">7\.</span></span> <span data-ttu-id="f1832-151">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f1832-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1832-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f1832-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="f1832-153">1\.</span><span class="sxs-lookup"><span data-stu-id="f1832-153">1\.</span></span> <span data-ttu-id="f1832-154">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="f1832-155">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="f1832-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="f1832-156">2\.</span><span class="sxs-lookup"><span data-stu-id="f1832-156">2\.</span></span> <span data-ttu-id="f1832-157">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="f1832-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="f1832-158">3\.</span><span class="sxs-lookup"><span data-stu-id="f1832-158">3\.</span></span> <span data-ttu-id="f1832-159">サイドバーで、[ **.NET Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="f1832-160">4\.</span><span class="sxs-lookup"><span data-stu-id="f1832-160">4\.</span></span> <span data-ttu-id="f1832-161">**Blazor サーバー アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="f1832-162">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="f1832-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="f1832-163">Blazor WebAssemblyのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="f1832-164">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="f1832-165">5\.</span><span class="sxs-lookup"><span data-stu-id="f1832-165">5\.</span></span> <span data-ttu-id="f1832-166">**ターゲットフレームワーク**を **.net Core 3.1**に設定し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-166">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="f1832-167">6\.</span><span class="sxs-lookup"><span data-stu-id="f1832-167">6\.</span></span> <span data-ttu-id="f1832-168">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1832-168">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f1832-169">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-169">Select **Create**.</span></span>

   <span data-ttu-id="f1832-170">7 \。</span><span class="sxs-lookup"><span data-stu-id="f1832-170">7\.</span></span> <span data-ttu-id="f1832-171">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-171">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f1832-172">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-172">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f1832-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1832-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="f1832-174">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f1832-175">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f1832-176">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="f1832-177">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f1832-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="f1832-178">最新の[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-178">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="f1832-179">必要に応じて[Blazor WebAssembly テンプレート](xref:blazor/hosting-models#blazor-webassembly)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-179">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="f1832-180">[.NET Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-180">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="f1832-181">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-181">Run the following command in a command shell.</span></span> <span data-ttu-id="f1832-182">[Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor WebAssembly はプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="f1832-182">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="f1832-183">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f1832-183">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1832-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1832-184">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="f1832-185">1\.</span><span class="sxs-lookup"><span data-stu-id="f1832-185">1\.</span></span> <span data-ttu-id="f1832-186">**ASP.NET と Web 開発**ワークロードを使用して、最新の[Visual Studio](https://visualstudio.com/vs/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-186">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f1832-187">2\.</span><span class="sxs-lookup"><span data-stu-id="f1832-187">2\.</span></span> <span data-ttu-id="f1832-188">必要に応じて、アプリ開発 Blazor のための**ASP.NET および Web 開発**ワークロードと共に[Visual Studio 16.4 Preview 2 以降](https://visualstudio.microsoft.com/vs/preview/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-188">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="f1832-189">3\.</span><span class="sxs-lookup"><span data-stu-id="f1832-189">3\.</span></span> <span data-ttu-id="f1832-190">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1832-190">Create a new project.</span></span>

   <span data-ttu-id="f1832-191">4\.</span><span class="sxs-lookup"><span data-stu-id="f1832-191">4\.</span></span> <span data-ttu-id="f1832-192">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-192">Select **Blazor App**.</span></span> <span data-ttu-id="f1832-193">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-193">Select **Next**.</span></span>

   <span data-ttu-id="f1832-194">5\.</span><span class="sxs-lookup"><span data-stu-id="f1832-194">5\.</span></span> <span data-ttu-id="f1832-195">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="f1832-195">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f1832-196">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1832-196">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f1832-197">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-197">Select **Create**.</span></span>

   <span data-ttu-id="f1832-198">6\.</span><span class="sxs-lookup"><span data-stu-id="f1832-198">6\.</span></span> <span data-ttu-id="f1832-199">Blazor WebAssembly については、 **Blazor WebAssembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-199">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f1832-200">Blazor サーバーのエクスペリエンスについては、 **Blazor サーバー アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-200">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f1832-201">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-201">Select **Create**.</span></span> <span data-ttu-id="f1832-202">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-202">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="f1832-203">7 \。</span><span class="sxs-lookup"><span data-stu-id="f1832-203">7\.</span></span> <span data-ttu-id="f1832-204">**F5 キー**を押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-204">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f1832-205">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="f1832-205">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="f1832-206">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="f1832-206">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1832-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1832-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="f1832-208">1\.</span><span class="sxs-lookup"><span data-stu-id="f1832-208">1\.</span></span> <span data-ttu-id="f1832-209">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="f1832-209">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="f1832-210">2\.</span><span class="sxs-lookup"><span data-stu-id="f1832-210">2\.</span></span> <span data-ttu-id="f1832-211">[C# Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-211">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="f1832-212">3\.</span><span class="sxs-lookup"><span data-stu-id="f1832-212">3\.</span></span> <span data-ttu-id="f1832-213">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-213">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="f1832-214">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-214">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="f1832-215">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-215">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="f1832-216">4\.</span><span class="sxs-lookup"><span data-stu-id="f1832-216">4\.</span></span> <span data-ttu-id="f1832-217">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f1832-217">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="f1832-218">5\.</span><span class="sxs-lookup"><span data-stu-id="f1832-218">5\.</span></span> <span data-ttu-id="f1832-219">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="f1832-219">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f1832-220">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-220">Select **Yes**.</span></span>

   <span data-ttu-id="f1832-221">6\.</span><span class="sxs-lookup"><span data-stu-id="f1832-221">6\.</span></span> <span data-ttu-id="f1832-222">Blazor サーバー アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-222">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="f1832-223">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-223">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="f1832-224">7 \。</span><span class="sxs-lookup"><span data-stu-id="f1832-224">7\.</span></span> <span data-ttu-id="f1832-225">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f1832-225">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1832-226">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f1832-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="f1832-227">1\.</span><span class="sxs-lookup"><span data-stu-id="f1832-227">1\.</span></span> <span data-ttu-id="f1832-228">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1832-228">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="f1832-229">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="f1832-229">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="f1832-230">2\.</span><span class="sxs-lookup"><span data-stu-id="f1832-230">2\.</span></span> <span data-ttu-id="f1832-231">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="f1832-231">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="f1832-232">3\.</span><span class="sxs-lookup"><span data-stu-id="f1832-232">3\.</span></span> <span data-ttu-id="f1832-233">サイドバーで、[ **.NET Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-233">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="f1832-234">4\.</span><span class="sxs-lookup"><span data-stu-id="f1832-234">4\.</span></span> <span data-ttu-id="f1832-235">**Blazor サーバー アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-235">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="f1832-236">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="f1832-236">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="f1832-237">Blazor WebAssemblyのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-237">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="f1832-238">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-238">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="f1832-239">5\.</span><span class="sxs-lookup"><span data-stu-id="f1832-239">5\.</span></span> <span data-ttu-id="f1832-240">**ターゲットフレームワーク**を **.net Core 3.0**に設定し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-240">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="f1832-241">6\.</span><span class="sxs-lookup"><span data-stu-id="f1832-241">6\.</span></span> <span data-ttu-id="f1832-242">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1832-242">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f1832-243">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-243">Select **Create**.</span></span>

   <span data-ttu-id="f1832-244">7 \。</span><span class="sxs-lookup"><span data-stu-id="f1832-244">7\.</span></span> <span data-ttu-id="f1832-245">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1832-245">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f1832-246">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-246">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f1832-247">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1832-247">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="f1832-248">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-248">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f1832-249">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-249">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f1832-250">2つの Blazor ホスティングモデル、 *Blazor サーバー* 、 *Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1832-250">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="f1832-251">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f1832-251">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="f1832-252">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1832-252">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="f1832-253">のホーム</span><span class="sxs-lookup"><span data-stu-id="f1832-253">Home</span></span>
* <span data-ttu-id="f1832-254">カウンター</span><span class="sxs-lookup"><span data-stu-id="f1832-254">Counter</span></span>
* <span data-ttu-id="f1832-255">データのフェッチ</span><span class="sxs-lookup"><span data-stu-id="f1832-255">Fetch data</span></span>

<span data-ttu-id="f1832-256">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="f1832-256">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f1832-257">通常、web ページのカウンターを増やすには JavaScript を記述する必要がありC#ますが、Blazor 使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1832-257">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="f1832-258">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f1832-258">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="f1832-259">上部の `@page` ディレクティブで指定されているように、ブラウザーでの `/counter` の要求によって、`Counter` コンポーネントがそのコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="f1832-259">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="f1832-260">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1832-260">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="f1832-261">**[クリックし**てください] ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f1832-261">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="f1832-262">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="f1832-262">The `onclick` event is fired.</span></span>
* <span data-ttu-id="f1832-263">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f1832-263">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="f1832-264">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="f1832-264">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="f1832-265">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1832-265">The component is rendered again.</span></span>

<span data-ttu-id="f1832-266">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="f1832-266">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="f1832-267">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="f1832-267">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="f1832-268">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加して、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1832-268">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="f1832-269">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f1832-269">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="f1832-270">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-270">Run the app.</span></span> <span data-ttu-id="f1832-271">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="f1832-271">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="f1832-272">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="f1832-272">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="f1832-273">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="f1832-273">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="f1832-274">`[Parameter]` 属性を持つ `IncrementAmount` のパブリックプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1832-274">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="f1832-275">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="f1832-275">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="f1832-276">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f1832-276">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="f1832-277">属性を使用して、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="f1832-277">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="f1832-278">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f1832-278">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="f1832-279">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1832-279">Run the app.</span></span> <span data-ttu-id="f1832-280">`Index` コンポーネントには、 **[クリックし**てください] ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="f1832-280">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f1832-281">`/counter` の `Counter` コンポーネント (*Counter*) は、1つずつ増加し続けます。</span><span class="sxs-lookup"><span data-stu-id="f1832-281">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f1832-282">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="f1832-282">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="f1832-283">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f1832-283">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
