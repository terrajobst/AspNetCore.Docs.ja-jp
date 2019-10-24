---
title: ASP.NET Core Blazor を使ってみる
author: guardrex
description: 好みのツールで Blazor アプリを構築して、Blazor の使用を開始しましょう。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: blazor/get-started
ms.openlocfilehash: 80ff7b42a44e722dd27bc4fde53a066863448e10
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779123"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="9d56a-103">ASP.NET Core Blazor を使ってみる</span><span class="sxs-lookup"><span data-stu-id="9d56a-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="9d56a-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9d56a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="9d56a-105">Blazor を使ってみる:</span><span class="sxs-lookup"><span data-stu-id="9d56a-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="9d56a-106">[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="9d56a-107">コマンドシェルで次のコマンドを実行して、 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="9d56a-108">[Blazor](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージはプレビュー版ですが、Blazor WebAssembly はプレビュー段階です。</span><span class="sxs-lookup"><span data-stu-id="9d56a-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="9d56a-109">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9d56a-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d56a-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="9d56a-111">1\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-111">1\.</span></span> <span data-ttu-id="9d56a-112">**ASP.NET と web 開発**ワークロードを使用して、 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/)以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="9d56a-113">2\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-113">2\.</span></span> <span data-ttu-id="9d56a-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-114">Create a new project.</span></span>

   <span data-ttu-id="9d56a-115">3\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-115">3\.</span></span> <span data-ttu-id="9d56a-116">**[Blazor App]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-116">Select **Blazor App**.</span></span> <span data-ttu-id="9d56a-117">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-117">Select **Next**.</span></span>

   <span data-ttu-id="9d56a-118">4\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-118">4\.</span></span> <span data-ttu-id="9d56a-119">**プロジェクト名** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="9d56a-120">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="9d56a-121">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-121">Select **Create**.</span></span>

   <span data-ttu-id="9d56a-122">5\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-122">5\.</span></span> <span data-ttu-id="9d56a-123">Blazor WebAssembly エクスペリエンスについては、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="9d56a-124">Blazor サーバーエクスペリエンスの場合は、 **Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="9d56a-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-125">Select **Create**.</span></span> <span data-ttu-id="9d56a-126">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、<xref:blazor/hosting-models> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="9d56a-127">6\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-127">6\.</span></span> <span data-ttu-id="9d56a-128">**Ctrl** + **F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9d56a-129">以前のプレビューリリースの ASP.NET Core Blazor (Preview 6 以前) 用に Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="9d56a-130">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="9d56a-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9d56a-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d56a-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="9d56a-132">1\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-132">1\.</span></span> <span data-ttu-id="9d56a-133">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="9d56a-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="9d56a-134">2\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-134">2\.</span></span> <span data-ttu-id="9d56a-135">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="9d56a-136">3\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-136">3\.</span></span> <span data-ttu-id="9d56a-137">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="9d56a-138">Blazor サーバーエクスペリエンスの場合は、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="9d56a-139">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、<xref:blazor/hosting-models> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="9d56a-140">4\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-140">4\.</span></span> <span data-ttu-id="9d56a-141">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="9d56a-142">5\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-142">5\.</span></span> <span data-ttu-id="9d56a-143">Blazor Server プロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="9d56a-144">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-144">Select **Yes**.</span></span>

   <span data-ttu-id="9d56a-145">6\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-145">6\.</span></span> <span data-ttu-id="9d56a-146">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="9d56a-147">Blazor WebAssembly を使用する場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="9d56a-148">7 \。</span><span class="sxs-lookup"><span data-stu-id="9d56a-148">7\.</span></span> <span data-ttu-id="9d56a-149">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9d56a-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9d56a-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="9d56a-151">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="9d56a-152">Blazor サーバーエクスペリエンスの場合は、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="9d56a-153">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、<xref:blazor/hosting-models> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="9d56a-154">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="9d56a-155">最新の[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)リリースをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="9d56a-156">必要に応じて、 [.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールし、コマンドシェルで次のコマンドを実行して、 [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly)テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="9d56a-157">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9d56a-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d56a-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="9d56a-159">1\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-159">1\.</span></span> <span data-ttu-id="9d56a-160">**ASP.NET と web 開発**ワークロードを使用して、最新の[Visual Studio](https://visualstudio.com/vs/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="9d56a-161">2\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-161">2\.</span></span> <span data-ttu-id="9d56a-162">必要に応じて、 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/)以降を、Blazor WebAssembly アプリ開発のための**および web 開発**ワークロードと共にインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="9d56a-163">3\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-163">3\.</span></span> <span data-ttu-id="9d56a-164">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-164">Create a new project.</span></span>

   <span data-ttu-id="9d56a-165">4\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-165">4\.</span></span> <span data-ttu-id="9d56a-166">**[Blazor App]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-166">Select **Blazor App**.</span></span> <span data-ttu-id="9d56a-167">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-167">Select **Next**.</span></span>

   <span data-ttu-id="9d56a-168">5\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-168">5\.</span></span> <span data-ttu-id="9d56a-169">**プロジェクト名** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="9d56a-170">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="9d56a-171">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-171">Select **Create**.</span></span>

   <span data-ttu-id="9d56a-172">6\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-172">6\.</span></span> <span data-ttu-id="9d56a-173">Blazor WebAssembly エクスペリエンスについては、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="9d56a-174">Blazor サーバーエクスペリエンスの場合は、 **Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="9d56a-175">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-175">Select **Create**.</span></span> <span data-ttu-id="9d56a-176">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、<xref:blazor/hosting-models> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="9d56a-177">7 \。</span><span class="sxs-lookup"><span data-stu-id="9d56a-177">7\.</span></span> <span data-ttu-id="9d56a-178">**F5 キー**を押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9d56a-179">以前のプレビューリリースの ASP.NET Core Blazor (Preview 6 以前) 用に Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="9d56a-180">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="9d56a-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9d56a-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d56a-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="9d56a-182">1\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-182">1\.</span></span> <span data-ttu-id="9d56a-183">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="9d56a-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="9d56a-184">2\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-184">2\.</span></span> <span data-ttu-id="9d56a-185">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="9d56a-186">3\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-186">3\.</span></span> <span data-ttu-id="9d56a-187">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="9d56a-188">Blazor サーバーエクスペリエンスの場合は、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="9d56a-189">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、<xref:blazor/hosting-models> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="9d56a-190">4\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-190">4\.</span></span> <span data-ttu-id="9d56a-191">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="9d56a-192">5\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-192">5\.</span></span> <span data-ttu-id="9d56a-193">Blazor Server プロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="9d56a-194">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-194">Select **Yes**.</span></span>

   <span data-ttu-id="9d56a-195">6\.</span><span class="sxs-lookup"><span data-stu-id="9d56a-195">6\.</span></span> <span data-ttu-id="9d56a-196">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="9d56a-197">Blazor WebAssembly を使用する場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="9d56a-198">7 \。</span><span class="sxs-lookup"><span data-stu-id="9d56a-198">7\.</span></span> <span data-ttu-id="9d56a-199">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-199">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9d56a-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9d56a-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="9d56a-201">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="9d56a-202">Blazor サーバーエクスペリエンスの場合は、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="9d56a-203">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、<xref:blazor/hosting-models> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d56a-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="9d56a-204">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="9d56a-205">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="9d56a-206">Home</span><span class="sxs-lookup"><span data-stu-id="9d56a-206">Home</span></span>
* <span data-ttu-id="9d56a-207">カウンター</span><span class="sxs-lookup"><span data-stu-id="9d56a-207">Counter</span></span>
* <span data-ttu-id="9d56a-208">データのフェッチ</span><span class="sxs-lookup"><span data-stu-id="9d56a-208">Fetch data</span></span>

<span data-ttu-id="9d56a-209">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="9d56a-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="9d56a-210">通常、web ページ内のカウンターを増やすには JavaScript を記述する必要がC#ありますが、Blazor を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="9d56a-211">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="9d56a-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="9d56a-212">上部の `@page` ディレクティブで指定されているように、ブラウザーで `/counter` に対する要求を行うと、`Counter` コンポーネントによってそのコンテンツがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="9d56a-213">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="9d56a-214">**[クリックし**てください] ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9d56a-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="9d56a-215">@No__t_0 イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="9d56a-216">`IncrementCount` メソッドが呼び出された場合。</span><span class="sxs-lookup"><span data-stu-id="9d56a-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="9d56a-217">@No__t_0 がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="9d56a-218">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-218">The component is rendered again.</span></span>

<span data-ttu-id="9d56a-219">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="9d56a-220">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="9d56a-221">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加して、`Counter` コンポーネントをアプリのホームページに追加します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="9d56a-222">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="9d56a-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="9d56a-223">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-223">Run the app.</span></span> <span data-ttu-id="9d56a-224">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="9d56a-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="9d56a-225">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="9d56a-226">@No__t_0 コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="9d56a-227">@No__t_1 属性を持つ `IncrementAmount` のパブリックプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="9d56a-228">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="9d56a-229">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="9d56a-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="9d56a-230">属性を使用して、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="9d56a-231">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="9d56a-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="9d56a-232">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d56a-232">Run the app.</span></span> <span data-ttu-id="9d56a-233">@No__t_0 コンポーネントには、 **[クリックし**てください] ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="9d56a-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="9d56a-234">@No__t_2 の `Counter` コンポーネント (*Counter*) は、1つずつ増加し続けます。</span><span class="sxs-lookup"><span data-stu-id="9d56a-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9d56a-235">次の手順</span><span class="sxs-lookup"><span data-stu-id="9d56a-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="9d56a-236">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d56a-236">Additional resources</span></span>

* <xref:signalr/introduction>
