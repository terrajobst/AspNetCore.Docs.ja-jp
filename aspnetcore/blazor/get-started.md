---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 選択したツールで Blazor アプリを構築して、Blazor を開始しましょう。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: d356a06849f54434c492dc68f57f7edc8805de22
ms.sourcegitcommit: 5974e3e66dab3398ecf2324fbb82a9c5636f70de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74778779"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="93c30-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="93c30-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="93c30-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="93c30-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="93c30-105">Blazorの概要:</span><span class="sxs-lookup"><span data-stu-id="93c30-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="93c30-106">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="93c30-107">必要に応じて[Blazor Webasテンプレート](xref:blazor/hosting-models#blazor-webassembly)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="93c30-108">[.NET Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="93c30-109">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-109">Run the following command in a command shell.</span></span> <span data-ttu-id="93c30-110">[BlazorAspNetCore です。テンプレート](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor Webasはプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="93c30-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="93c30-111">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="93c30-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="93c30-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93c30-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="93c30-113">1 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-113">1\.</span></span> <span data-ttu-id="93c30-114">**ASP.NET と web 開発**ワークロードを使用して、 [Visual Studio 16.4](https://visualstudio.microsoft.com/vs/preview/)以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="93c30-115">2 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-115">2\.</span></span> <span data-ttu-id="93c30-116">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="93c30-116">Create a new project.</span></span>

   <span data-ttu-id="93c30-117">3 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-117">3\.</span></span> <span data-ttu-id="93c30-118">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-118">Select **Blazor App**.</span></span> <span data-ttu-id="93c30-119">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-119">Select **Next**.</span></span>

   <span data-ttu-id="93c30-120">4 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-120">4\.</span></span> <span data-ttu-id="93c30-121">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="93c30-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="93c30-122">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="93c30-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="93c30-123">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-123">Select **Create**.</span></span>

   <span data-ttu-id="93c30-124">5 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-124">5\.</span></span> <span data-ttu-id="93c30-125">Blazor webassembly については、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="93c30-126">Blazor サーバーのエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="93c30-127">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-127">Select **Create**.</span></span> <span data-ttu-id="93c30-128">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93c30-129">6 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-129">6\.</span></span> <span data-ttu-id="93c30-130">**Ctrl** + **F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93c30-131">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="93c30-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="93c30-132">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="93c30-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="93c30-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93c30-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="93c30-134">1 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-134">1\.</span></span> <span data-ttu-id="93c30-135">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="93c30-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="93c30-136">2 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-136">2\.</span></span> <span data-ttu-id="93c30-137">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="93c30-138">3 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-138">3\.</span></span> <span data-ttu-id="93c30-139">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="93c30-140">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="93c30-141">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93c30-142">4 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-142">4\.</span></span> <span data-ttu-id="93c30-143">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="93c30-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="93c30-144">5 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-144">5\.</span></span> <span data-ttu-id="93c30-145">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="93c30-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="93c30-146">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-146">Select **Yes**.</span></span>

   <span data-ttu-id="93c30-147">6 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-147">6\.</span></span> <span data-ttu-id="93c30-148">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="93c30-149">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="93c30-150">7 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-150">7\.</span></span> <span data-ttu-id="93c30-151">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="93c30-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="93c30-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="93c30-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="93c30-153">1 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-153">1\.</span></span> <span data-ttu-id="93c30-154">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="93c30-155">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="93c30-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="93c30-156">2 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-156">2\.</span></span> <span data-ttu-id="93c30-157">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="93c30-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="93c30-158">3 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-158">3\.</span></span> <span data-ttu-id="93c30-159">サイドバーで、[ **.Net Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="93c30-160">4 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-160">4\.</span></span> <span data-ttu-id="93c30-161">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="93c30-162">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="93c30-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="93c30-163">Blazor Webasのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="93c30-164">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="93c30-165">5 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-165">5\.</span></span> <span data-ttu-id="93c30-166">**ターゲットフレームワーク**は、既定で **.net core 3.0** (または、3.1 Preview SDK がインストールされている場合は **.net core 3.1** ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="93c30-166">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="93c30-167">フレームワークを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-167">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="93c30-168">6 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-168">6\.</span></span> <span data-ttu-id="93c30-169">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="93c30-169">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="93c30-170">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-170">Select **Create**.</span></span>

   <span data-ttu-id="93c30-171">7 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-171">7\.</span></span> <span data-ttu-id="93c30-172">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-172">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="93c30-173">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-173">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="93c30-174">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="93c30-174">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="93c30-175">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-175">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93c30-176">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-176">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93c30-177">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-177">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93c30-178">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="93c30-178">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="93c30-179">最新の[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-179">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="93c30-180">必要に応じて[Blazor Webasテンプレート](xref:blazor/hosting-models#blazor-webassembly)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-180">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="93c30-181">[.NET Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-181">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="93c30-182">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-182">Run the following command in a command shell.</span></span> <span data-ttu-id="93c30-183">[BlazorAspNetCore です。テンプレート](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor Webasはプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="93c30-183">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="93c30-184">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="93c30-184">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="93c30-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93c30-185">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="93c30-186">1 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-186">1\.</span></span> <span data-ttu-id="93c30-187">**ASP.NET と web 開発**ワークロードを使用して、最新の[Visual Studio](https://visualstudio.com/vs/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-187">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="93c30-188">2 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-188">2\.</span></span> <span data-ttu-id="93c30-189">必要に応じて、アプリ開発 Blazor のための**ASP.NET および web 開発**ワークロードと共に[Visual Studio 16.4 Preview 2 以降](https://visualstudio.microsoft.com/vs/preview/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-189">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="93c30-190">3 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-190">3\.</span></span> <span data-ttu-id="93c30-191">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="93c30-191">Create a new project.</span></span>

   <span data-ttu-id="93c30-192">4 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-192">4\.</span></span> <span data-ttu-id="93c30-193">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-193">Select **Blazor App**.</span></span> <span data-ttu-id="93c30-194">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-194">Select **Next**.</span></span>

   <span data-ttu-id="93c30-195">5 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-195">5\.</span></span> <span data-ttu-id="93c30-196">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="93c30-196">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="93c30-197">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="93c30-197">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="93c30-198">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-198">Select **Create**.</span></span>

   <span data-ttu-id="93c30-199">6 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-199">6\.</span></span> <span data-ttu-id="93c30-200">Blazor webassembly については、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-200">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="93c30-201">Blazor サーバーのエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-201">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="93c30-202">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-202">Select **Create**.</span></span> <span data-ttu-id="93c30-203">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93c30-204">7 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-204">7\.</span></span> <span data-ttu-id="93c30-205">**F5 キー**を押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-205">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93c30-206">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="93c30-206">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="93c30-207">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="93c30-207">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="93c30-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93c30-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="93c30-209">1 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-209">1\.</span></span> <span data-ttu-id="93c30-210">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="93c30-210">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="93c30-211">2 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-211">2\.</span></span> <span data-ttu-id="93c30-212">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-212">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="93c30-213">3 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-213">3\.</span></span> <span data-ttu-id="93c30-214">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-214">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="93c30-215">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-215">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="93c30-216">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-216">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93c30-217">4 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-217">4\.</span></span> <span data-ttu-id="93c30-218">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="93c30-218">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="93c30-219">5 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-219">5\.</span></span> <span data-ttu-id="93c30-220">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="93c30-220">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="93c30-221">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-221">Select **Yes**.</span></span>

   <span data-ttu-id="93c30-222">6 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-222">6\.</span></span> <span data-ttu-id="93c30-223">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-223">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="93c30-224">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-224">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="93c30-225">7 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-225">7\.</span></span> <span data-ttu-id="93c30-226">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="93c30-226">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="93c30-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="93c30-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="93c30-228">1 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-228">1\.</span></span> <span data-ttu-id="93c30-229">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="93c30-229">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="93c30-230">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="93c30-230">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="93c30-231">2 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-231">2\.</span></span> <span data-ttu-id="93c30-232">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="93c30-232">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="93c30-233">3 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-233">3\.</span></span> <span data-ttu-id="93c30-234">サイドバーで、[ **.Net Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-234">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="93c30-235">4 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-235">4\.</span></span> <span data-ttu-id="93c30-236">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-236">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="93c30-237">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="93c30-237">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="93c30-238">Blazor Webasのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-238">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="93c30-239">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-239">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="93c30-240">5 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-240">5\.</span></span> <span data-ttu-id="93c30-241">**ターゲットフレームワーク**は、既定で **.net core 3.0** (または、3.1 Preview SDK がインストールされている場合は **.net core 3.1** ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="93c30-241">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="93c30-242">フレームワークを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-242">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="93c30-243">6 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-243">6\.</span></span> <span data-ttu-id="93c30-244">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="93c30-244">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="93c30-245">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-245">Select **Create**.</span></span>

   <span data-ttu-id="93c30-246">7 \。</span><span class="sxs-lookup"><span data-stu-id="93c30-246">7\.</span></span> <span data-ttu-id="93c30-247">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="93c30-247">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="93c30-248">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-248">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="93c30-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="93c30-249">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="93c30-250">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-250">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93c30-251">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-251">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93c30-252">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93c30-252">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93c30-253">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="93c30-253">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="93c30-254">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="93c30-254">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="93c30-255">Home</span><span class="sxs-lookup"><span data-stu-id="93c30-255">Home</span></span>
* <span data-ttu-id="93c30-256">Counter</span><span class="sxs-lookup"><span data-stu-id="93c30-256">Counter</span></span>
* <span data-ttu-id="93c30-257">Fetch data</span><span class="sxs-lookup"><span data-stu-id="93c30-257">Fetch data</span></span>

<span data-ttu-id="93c30-258">Counter ページ上で **[Click me]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="93c30-258">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="93c30-259">通常、web ページのカウンターを増やすには JavaScript を記述する必要がありC#ますが、Blazor 使用できます。</span><span class="sxs-lookup"><span data-stu-id="93c30-259">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="93c30-260">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="93c30-260">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="93c30-261">上部の `@page` ディレクティブで指定されているように、ブラウザーでの `/counter` の要求によって、`Counter` コンポーネントがそのコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="93c30-261">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="93c30-262">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="93c30-262">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="93c30-263">**Click me** ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="93c30-263">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="93c30-264">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="93c30-264">The `onclick` event is fired.</span></span>
* <span data-ttu-id="93c30-265">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="93c30-265">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="93c30-266">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="93c30-266">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="93c30-267">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="93c30-267">The component is rendered again.</span></span>

<span data-ttu-id="93c30-268">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="93c30-268">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="93c30-269">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="93c30-269">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="93c30-270">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加して、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="93c30-270">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="93c30-271">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="93c30-271">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="93c30-272">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-272">Run the app.</span></span> <span data-ttu-id="93c30-273">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="93c30-273">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="93c30-274">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="93c30-274">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="93c30-275">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="93c30-275">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="93c30-276">`[Parameter]` 属性を持つ `IncrementAmount` のパブリックプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="93c30-276">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="93c30-277">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="93c30-277">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="93c30-278">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="93c30-278">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="93c30-279">属性を使用して、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="93c30-279">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="93c30-280">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="93c30-280">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="93c30-281">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="93c30-281">Run the app.</span></span> <span data-ttu-id="93c30-282">`Index` コンポーネントには、 **Click me** ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="93c30-282">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="93c30-283">`/counter` の `Counter` コンポーネント (*Counter*) は、1つずつ増加し続けます。</span><span class="sxs-lookup"><span data-stu-id="93c30-283">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="93c30-284">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="93c30-284">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="93c30-285">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="93c30-285">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
