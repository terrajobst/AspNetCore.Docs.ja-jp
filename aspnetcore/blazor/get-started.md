---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 選択したツールで Blazor アプリを構築して、Blazor を開始しましょう。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: bd33d874b3d6122f2ab820e9b147b0e62ba03a58
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869581"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="135ef-103">ASP.NET Core Blazor を使ってみる</span><span class="sxs-lookup"><span data-stu-id="135ef-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="135ef-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="135ef-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="135ef-105">Blazor を使ってみる:</span><span class="sxs-lookup"><span data-stu-id="135ef-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="135ef-106">[.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="135ef-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="135ef-107">必要に応じて、 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="135ef-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="135ef-108">[.NET Core 3.1 以降 (プレビュー) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="135ef-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="135ef-109">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-109">Run the following command in a command shell.</span></span> <span data-ttu-id="135ef-110">[Blazor](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)パッケージはプレビュー版ですが、Blazor WebAssembly はプレビュー段階です。</span><span class="sxs-lookup"><span data-stu-id="135ef-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
   ```

1. <span data-ttu-id="135ef-111">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="135ef-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="135ef-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="135ef-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="135ef-113">1\.</span><span class="sxs-lookup"><span data-stu-id="135ef-113">1\.</span></span> <span data-ttu-id="135ef-114">**ASP.NET と web 開発**ワークロードを使用して、 [Visual Studio 2019 バージョン 16.4](https://visualstudio.microsoft.com/vs/preview/)以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="135ef-114">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="135ef-115">2\.</span><span class="sxs-lookup"><span data-stu-id="135ef-115">2\.</span></span> <span data-ttu-id="135ef-116">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="135ef-116">Create a new project.</span></span>

   <span data-ttu-id="135ef-117">3\.</span><span class="sxs-lookup"><span data-stu-id="135ef-117">3\.</span></span> <span data-ttu-id="135ef-118">**[Blazor App]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-118">Select **Blazor App**.</span></span> <span data-ttu-id="135ef-119">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-119">Select **Next**.</span></span>

   <span data-ttu-id="135ef-120">4\.</span><span class="sxs-lookup"><span data-stu-id="135ef-120">4\.</span></span> <span data-ttu-id="135ef-121">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="135ef-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="135ef-122">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="135ef-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="135ef-123">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-123">Select **Create**.</span></span>

   <span data-ttu-id="135ef-124">5\.</span><span class="sxs-lookup"><span data-stu-id="135ef-124">5\.</span></span> <span data-ttu-id="135ef-125">Blazor WebAssembly エクスペリエンスについては、 **Blazor Webassembly**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="135ef-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="135ef-126">Blazor サーバーエクスペリエンスの場合は、 **Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="135ef-127">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-127">Select **Create**.</span></span> <span data-ttu-id="135ef-128">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="135ef-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="135ef-129">6\.</span><span class="sxs-lookup"><span data-stu-id="135ef-129">6\.</span></span> <span data-ttu-id="135ef-130">**Ctrl**+**F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="135ef-131">以前のプレビューリリースの ASP.NET Core Blazor (Preview 6 以前) 用に Blazor Visual Studio 拡張機能をインストールした場合は、拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="135ef-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="135ef-132">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="135ef-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="135ef-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="135ef-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="135ef-134">1\.</span><span class="sxs-lookup"><span data-stu-id="135ef-134">1\.</span></span> <span data-ttu-id="135ef-135">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="135ef-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="135ef-136">2\.</span><span class="sxs-lookup"><span data-stu-id="135ef-136">2\.</span></span> <span data-ttu-id="135ef-137">[C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="135ef-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="135ef-138">3\.</span><span class="sxs-lookup"><span data-stu-id="135ef-138">3\.</span></span> <span data-ttu-id="135ef-139">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="135ef-140">Blazor サーバーエクスペリエンスの場合は、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="135ef-141">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="135ef-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="135ef-142">4\.</span><span class="sxs-lookup"><span data-stu-id="135ef-142">4\.</span></span> <span data-ttu-id="135ef-143">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="135ef-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="135ef-144">5\.</span><span class="sxs-lookup"><span data-stu-id="135ef-144">5\.</span></span> <span data-ttu-id="135ef-145">Blazor Server プロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="135ef-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="135ef-146">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-146">Select **Yes**.</span></span>

   <span data-ttu-id="135ef-147">6\.</span><span class="sxs-lookup"><span data-stu-id="135ef-147">6\.</span></span> <span data-ttu-id="135ef-148">Blazor Server アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="135ef-149">Blazor WebAssembly を使用している場合は、アプリのプロジェクトフォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="135ef-150">7 \。</span><span class="sxs-lookup"><span data-stu-id="135ef-150">7\.</span></span> <span data-ttu-id="135ef-151">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="135ef-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="135ef-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="135ef-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="135ef-153">1\.</span><span class="sxs-lookup"><span data-stu-id="135ef-153">1\.</span></span> <span data-ttu-id="135ef-154">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="135ef-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="135ef-155">2\.</span><span class="sxs-lookup"><span data-stu-id="135ef-155">2\.</span></span> <span data-ttu-id="135ef-156">**ファイル** > **新しいソリューション**を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="135ef-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="135ef-157">3\.</span><span class="sxs-lookup"><span data-stu-id="135ef-157">3\.</span></span> <span data-ttu-id="135ef-158">サイドバーで、[ **.NET Core** > **アプリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="135ef-159">4\.</span><span class="sxs-lookup"><span data-stu-id="135ef-159">4\.</span></span> <span data-ttu-id="135ef-160">**Blazor Server アプリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="135ef-161">現時点では、Visual Studio for Mac で使用できるのは Blazor Server テンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="135ef-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="135ef-162">Blazor WebAssembly については、 **[.NET Core CLI]** タブの指示に従ってください。Blazor Server テンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="135ef-163">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="135ef-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="135ef-164">5\.</span><span class="sxs-lookup"><span data-stu-id="135ef-164">5\.</span></span> <span data-ttu-id="135ef-165">**ターゲットフレームワーク**を **.net Core 3.1**に設定し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="135ef-166">6\.</span><span class="sxs-lookup"><span data-stu-id="135ef-166">6\.</span></span> <span data-ttu-id="135ef-167">[Project Name] \ (**プロジェクト名**\) フィールドに、アプリに `WebApplication1`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="135ef-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="135ef-168">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-168">Select **Create**.</span></span>

   <span data-ttu-id="135ef-169">7 \。</span><span class="sxs-lookup"><span data-stu-id="135ef-169">7\.</span></span> <span data-ttu-id="135ef-170">*デバッガーを使用せず*にアプリを実行するには、[**実行** > **デバッグなしで実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="135ef-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="135ef-171">**デバッグを開始**してアプリを実行し、*デバッガーで*アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="135ef-172">開発証明書を信頼するように求めるメッセージが表示された場合は、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="135ef-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="135ef-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="135ef-174">Blazor WebAssembly を実現するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="135ef-175">Blazor サーバーエクスペリエンスの場合は、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="135ef-176">*Blazor Server*と*Blazor Webassembly*の2つのホスティングモデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="135ef-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="135ef-177">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="135ef-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="135ef-178">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="135ef-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="135ef-179">のホーム</span><span class="sxs-lookup"><span data-stu-id="135ef-179">Home</span></span>
* <span data-ttu-id="135ef-180">カウンター</span><span class="sxs-lookup"><span data-stu-id="135ef-180">Counter</span></span>
* <span data-ttu-id="135ef-181">データのフェッチ</span><span class="sxs-lookup"><span data-stu-id="135ef-181">Fetch data</span></span>

<span data-ttu-id="135ef-182">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="135ef-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="135ef-183">通常、Web ページのカウンターを増やすには JavaScript を記述する必要がありますが、Blazor では C# を使用できます。</span><span class="sxs-lookup"><span data-stu-id="135ef-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="135ef-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="135ef-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="135ef-185">上部の `@page` ディレクティブで指定されているように、ブラウザーでの `/counter` の要求によって、`Counter` コンポーネントがそのコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="135ef-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="135ef-186">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="135ef-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="135ef-187">**[クリックし**てください] ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="135ef-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="135ef-188">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="135ef-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="135ef-189">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135ef-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="135ef-190">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="135ef-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="135ef-191">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="135ef-191">The component is rendered again.</span></span>

<span data-ttu-id="135ef-192">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="135ef-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="135ef-193">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="135ef-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="135ef-194">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加して、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="135ef-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="135ef-195">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="135ef-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="135ef-196">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-196">Run the app.</span></span> <span data-ttu-id="135ef-197">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="135ef-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="135ef-198">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="135ef-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="135ef-199">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="135ef-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="135ef-200">`[Parameter]` 属性を持つ `IncrementAmount` のパブリックプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="135ef-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="135ef-201">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="135ef-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="135ef-202">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="135ef-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="135ef-203">属性を使用して、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="135ef-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="135ef-204">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="135ef-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="135ef-205">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="135ef-205">Run the app.</span></span> <span data-ttu-id="135ef-206">`Index` コンポーネントには、 **[クリックし**てください] ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="135ef-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="135ef-207">`/counter` の `Counter` コンポーネント (*Counter*) は、1つずつ増加し続けます。</span><span class="sxs-lookup"><span data-stu-id="135ef-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="135ef-208">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="135ef-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="135ef-209">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="135ef-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
