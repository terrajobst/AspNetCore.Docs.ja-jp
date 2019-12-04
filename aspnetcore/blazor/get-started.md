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
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="913c9-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="913c9-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="913c9-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="913c9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="913c9-105">Blazorの概要:</span><span class="sxs-lookup"><span data-stu-id="913c9-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="913c9-106">[.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="913c9-107">必要に応じて[Blazor Webasテンプレート](xref:blazor/hosting-models#blazor-webassembly)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="913c9-108">[.Net Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="913c9-109">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-109">Run the following command in a command shell.</span></span> <span data-ttu-id="913c9-110">[BlazorAspNetCore です。テンプレート](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor Webasはプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="913c9-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="913c9-111">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="913c9-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="913c9-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="913c9-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="913c9-113">1 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-113">1\.</span></span> <span data-ttu-id="913c9-114">**ASP.NET と web 開発**ワークロードを使用して、 [Visual Studio 16.4](https://visualstudio.microsoft.com/vs/preview/)以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="913c9-115">2 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-115">2\.</span></span> <span data-ttu-id="913c9-116">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="913c9-116">Create a new project.</span></span>

   <span data-ttu-id="913c9-117">3 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-117">3\.</span></span> <span data-ttu-id="913c9-118">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-118">Select **Blazor App**.</span></span> <span data-ttu-id="913c9-119">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-119">Select **Next**.</span></span>

   <span data-ttu-id="913c9-120">4 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-120">4\.</span></span> <span data-ttu-id="913c9-121">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="913c9-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="913c9-122">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="913c9-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="913c9-123">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-123">Select **Create**.</span></span>

   <span data-ttu-id="913c9-124">5 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-124">5\.</span></span> <span data-ttu-id="913c9-125">Blazor webassembly については、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="913c9-126">Blazor サーバーのエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="913c9-127">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-127">Select **Create**.</span></span> <span data-ttu-id="913c9-128">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="913c9-129">6 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-129">6\.</span></span> <span data-ttu-id="913c9-130">**Ctrl** + **F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="913c9-131">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="913c9-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="913c9-132">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="913c9-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="913c9-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="913c9-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="913c9-134">1 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-134">1\.</span></span> <span data-ttu-id="913c9-135">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="913c9-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="913c9-136">2 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-136">2\.</span></span> <span data-ttu-id="913c9-137">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="913c9-138">3 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-138">3\.</span></span> <span data-ttu-id="913c9-139">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="913c9-140">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="913c9-141">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="913c9-142">4 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-142">4\.</span></span> <span data-ttu-id="913c9-143">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="913c9-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="913c9-144">5 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-144">5\.</span></span> <span data-ttu-id="913c9-145">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="913c9-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="913c9-146">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-146">Select **Yes**.</span></span>

   <span data-ttu-id="913c9-147">6 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-147">6\.</span></span> <span data-ttu-id="913c9-148">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="913c9-149">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="913c9-150">7 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-150">7\.</span></span> <span data-ttu-id="913c9-151">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="913c9-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="913c9-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="913c9-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="913c9-153">1 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-153">1\.</span></span> <span data-ttu-id="913c9-154">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="913c9-155">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="913c9-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="913c9-156">2 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-156">2\.</span></span> <span data-ttu-id="913c9-157">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="913c9-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="913c9-158">3 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-158">3\.</span></span> <span data-ttu-id="913c9-159">サイドバーで、[ **.Net Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="913c9-160">4 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-160">4\.</span></span> <span data-ttu-id="913c9-161">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="913c9-162">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="913c9-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="913c9-163">Blazor Webasのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="913c9-164">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="913c9-165">5 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-165">5\.</span></span> <span data-ttu-id="913c9-166">**ターゲットフレームワーク**は、既定で **.net core 3.0** (または、3.1 Preview SDK がインストールされている場合は **.net core 3.1** ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="913c9-166">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="913c9-167">フレームワークを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-167">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="913c9-168">6 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-168">6\.</span></span> <span data-ttu-id="913c9-169">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="913c9-169">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="913c9-170">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-170">Select **Create**.</span></span>

   <span data-ttu-id="913c9-171">7 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-171">7\.</span></span> <span data-ttu-id="913c9-172">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-172">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="913c9-173">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-173">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="913c9-174">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="913c9-174">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="913c9-175">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-175">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="913c9-176">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-176">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="913c9-177">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-177">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="913c9-178">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="913c9-178">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="913c9-179">最新の[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-179">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="913c9-180">必要に応じて[Blazor Webasテンプレート](xref:blazor/hosting-models#blazor-webassembly)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-180">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="913c9-181">[.Net Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-181">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="913c9-182">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-182">Run the following command in a command shell.</span></span> <span data-ttu-id="913c9-183">[BlazorAspNetCore です。テンプレート](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージにはプレビューバージョンがありますが、プレビュー段階で Blazor Webasはプレビュー版です。</span><span class="sxs-lookup"><span data-stu-id="913c9-183">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="913c9-184">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="913c9-184">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="913c9-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="913c9-185">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="913c9-186">1 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-186">1\.</span></span> <span data-ttu-id="913c9-187">**ASP.NET と web 開発**ワークロードを使用して、最新の[Visual Studio](https://visualstudio.com/vs/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-187">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="913c9-188">2 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-188">2\.</span></span> <span data-ttu-id="913c9-189">必要に応じて、アプリ開発 Blazor のための**ASP.NET および web 開発**ワークロードと共に[Visual Studio 16.4 Preview 2 以降](https://visualstudio.microsoft.com/vs/preview/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-189">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="913c9-190">3 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-190">3\.</span></span> <span data-ttu-id="913c9-191">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="913c9-191">Create a new project.</span></span>

   <span data-ttu-id="913c9-192">4 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-192">4\.</span></span> <span data-ttu-id="913c9-193">**Blazor アプリ**を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-193">Select **Blazor App**.</span></span> <span data-ttu-id="913c9-194">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-194">Select **Next**.</span></span>

   <span data-ttu-id="913c9-195">5 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-195">5\.</span></span> <span data-ttu-id="913c9-196">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="913c9-196">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="913c9-197">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="913c9-197">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="913c9-198">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-198">Select **Create**.</span></span>

   <span data-ttu-id="913c9-199">6 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-199">6\.</span></span> <span data-ttu-id="913c9-200">Blazor webassembly については、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-200">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="913c9-201">Blazor サーバーのエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-201">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="913c9-202">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-202">Select **Create**.</span></span> <span data-ttu-id="913c9-203">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="913c9-204">7 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-204">7\.</span></span> <span data-ttu-id="913c9-205">**F5 キー**を押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-205">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="913c9-206">ASP.NET Core Blazor (Preview 6 以前) の以前のプレビューリリースに Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="913c9-206">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="913c9-207">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="913c9-207">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="913c9-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="913c9-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="913c9-209">1 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-209">1\.</span></span> <span data-ttu-id="913c9-210">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="913c9-210">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="913c9-211">2 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-211">2\.</span></span> <span data-ttu-id="913c9-212">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-212">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="913c9-213">3 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-213">3\.</span></span> <span data-ttu-id="913c9-214">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-214">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="913c9-215">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-215">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="913c9-216">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-216">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="913c9-217">4 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-217">4\.</span></span> <span data-ttu-id="913c9-218">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="913c9-218">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="913c9-219">5 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-219">5\.</span></span> <span data-ttu-id="913c9-220">Blazor サーバープロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="913c9-220">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="913c9-221">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-221">Select **Yes**.</span></span>

   <span data-ttu-id="913c9-222">6 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-222">6\.</span></span> <span data-ttu-id="913c9-223">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-223">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="913c9-224">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-224">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="913c9-225">7 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-225">7\.</span></span> <span data-ttu-id="913c9-226">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="913c9-226">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="913c9-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="913c9-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="913c9-228">1 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-228">1\.</span></span> <span data-ttu-id="913c9-229">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="913c9-229">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="913c9-230">[更新チャネルをプレビューに](/visualstudio/mac/install-preview)切り替えます。</span><span class="sxs-lookup"><span data-stu-id="913c9-230">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="913c9-231">2 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-231">2\.</span></span> <span data-ttu-id="913c9-232">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="913c9-232">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="913c9-233">3 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-233">3\.</span></span> <span data-ttu-id="913c9-234">サイドバーで、[ **.Net Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-234">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="913c9-235">4 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-235">4\.</span></span> <span data-ttu-id="913c9-236">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-236">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="913c9-237">現時点では、Visual Studio for Mac で使用できるのは Blazor サーバーテンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="913c9-237">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="913c9-238">Blazor Webasのエクスペリエンスについては、 **[.NET Core CLI]** タブの指示に従ってください。Blazor サーバーテンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-238">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="913c9-239">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-239">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="913c9-240">5 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-240">5\.</span></span> <span data-ttu-id="913c9-241">**ターゲットフレームワーク**は、既定で **.net core 3.0** (または、3.1 Preview SDK がインストールされている場合は **.net core 3.1** ) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="913c9-241">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="913c9-242">フレームワークを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-242">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="913c9-243">6 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-243">6\.</span></span> <span data-ttu-id="913c9-244">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="913c9-244">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="913c9-245">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-245">Select **Create**.</span></span>

   <span data-ttu-id="913c9-246">7 \。</span><span class="sxs-lookup"><span data-stu-id="913c9-246">7\.</span></span> <span data-ttu-id="913c9-247">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="913c9-247">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="913c9-248">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-248">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="913c9-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="913c9-249">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="913c9-250">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-250">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="913c9-251">Blazor サーバーのエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-251">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="913c9-252">2つの Blazor ホスティングモデル、 *Blazor Server* 、 *Blazor webasに*ついては、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="913c9-252">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="913c9-253">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="913c9-253">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="913c9-254">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="913c9-254">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="913c9-255">のホーム</span><span class="sxs-lookup"><span data-stu-id="913c9-255">Home</span></span>
* <span data-ttu-id="913c9-256">カウンター</span><span class="sxs-lookup"><span data-stu-id="913c9-256">Counter</span></span>
* <span data-ttu-id="913c9-257">データのフェッチ</span><span class="sxs-lookup"><span data-stu-id="913c9-257">Fetch data</span></span>

<span data-ttu-id="913c9-258">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="913c9-258">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="913c9-259">通常、web ページのカウンターを増やすには JavaScript を記述する必要がありC#ますが、Blazor 使用できます。</span><span class="sxs-lookup"><span data-stu-id="913c9-259">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="913c9-260">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="913c9-260">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="913c9-261">上部の `@page` ディレクティブで指定されているように、ブラウザーでの `/counter` の要求によって、`Counter` コンポーネントがそのコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="913c9-261">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="913c9-262">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="913c9-262">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="913c9-263">**[クリックし**てください] ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="913c9-263">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="913c9-264">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="913c9-264">The `onclick` event is fired.</span></span>
* <span data-ttu-id="913c9-265">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="913c9-265">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="913c9-266">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="913c9-266">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="913c9-267">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="913c9-267">The component is rendered again.</span></span>

<span data-ttu-id="913c9-268">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="913c9-268">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="913c9-269">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="913c9-269">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="913c9-270">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加して、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="913c9-270">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="913c9-271">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="913c9-271">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="913c9-272">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-272">Run the app.</span></span> <span data-ttu-id="913c9-273">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="913c9-273">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="913c9-274">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="913c9-274">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="913c9-275">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="913c9-275">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="913c9-276">`[Parameter]` 属性を持つ `IncrementAmount` のパブリックプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="913c9-276">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="913c9-277">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="913c9-277">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="913c9-278">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="913c9-278">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="913c9-279">属性を使用して、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="913c9-279">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="913c9-280">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="913c9-280">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="913c9-281">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="913c9-281">Run the app.</span></span> <span data-ttu-id="913c9-282">`Index` コンポーネントには、 **[クリックし**てください] ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="913c9-282">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="913c9-283">`/counter` の `Counter` コンポーネント (*Counter*) は、1つずつ増加し続けます。</span><span class="sxs-lookup"><span data-stu-id="913c9-283">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="913c9-284">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="913c9-284">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="913c9-285">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="913c9-285">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
