---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 希望のツールで Blazor アプリを構築することで、Blazor の利用を開始してください。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083244"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="7b598-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="7b598-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="7b598-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7b598-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7b598-105">Blazor の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="7b598-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="7b598-106">[.NET Core 3.1 SDK をインストールします](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="7b598-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="7b598-107">オプションで、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b598-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="7b598-108">[.NET Core 3.1 102 以降 (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b598-108">Install the [.NET Core 3.1.102 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="7b598-109">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-109">Run the following command in a command shell.</span></span> <span data-ttu-id="7b598-110">Blazor WebAssembly がプレビュー段階にある間、[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) パッケージにはプレビュー バージョンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="7b598-110">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > <span data-ttu-id="7b598-111">3\.2 Preview 2 Blazor WebAssembly テンプレートを使用するには、.NET Core SDK バージョン 3.1.102 以降が**必要です**。</span><span class="sxs-lookup"><span data-stu-id="7b598-111">.NET Core SDK version 3.1.102 or later is **required** to use the 3.2 Preview 2 Blazor WebAssembly template.</span></span> <span data-ttu-id="7b598-112">コマンド シェルで `dotnet --version` を実行して、インストールされている .NET Core SDK バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b598-112">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="7b598-113">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="7b598-113">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="7b598-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b598-114">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="7b598-115">1\.</span><span class="sxs-lookup"><span data-stu-id="7b598-115">1\.</span></span> <span data-ttu-id="7b598-116">**ASP.NET および Web 開発**ワークロードを含む [Visual Studio 2019 バージョン 16.4 以降](https://visualstudio.microsoft.com/vs/preview/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b598-116">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="7b598-117">2\.</span><span class="sxs-lookup"><span data-stu-id="7b598-117">2\.</span></span> <span data-ttu-id="7b598-118">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b598-118">Create a new project.</span></span>

   <span data-ttu-id="7b598-119">3\.</span><span class="sxs-lookup"><span data-stu-id="7b598-119">3\.</span></span> <span data-ttu-id="7b598-120">**[Blazor アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-120">Select **Blazor App**.</span></span> <span data-ttu-id="7b598-121">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-121">Select **Next**.</span></span>

   <span data-ttu-id="7b598-122">4\.</span><span class="sxs-lookup"><span data-stu-id="7b598-122">4\.</span></span> <span data-ttu-id="7b598-123">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="7b598-123">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7b598-124">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="7b598-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="7b598-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-125">Select **Create**.</span></span>

   <span data-ttu-id="7b598-126">5\.</span><span class="sxs-lookup"><span data-stu-id="7b598-126">5\.</span></span> <span data-ttu-id="7b598-127">Blazor WebAssembly エクスペリエンスについては、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-127">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="7b598-128">Blazor Server エクスペリエンスについては、 **[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-128">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="7b598-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-129">Select **Create**.</span></span> <span data-ttu-id="7b598-130">2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b598-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="7b598-131">Blazor WebAssembly テンプレートが存在しない場合は、前のステップに戻り、テンプレートを再インストールします。</span><span class="sxs-lookup"><span data-stu-id="7b598-131">If the Blazor WebAssembly template isn't present, return to the previous step and reinstall the template.</span></span>

   <span data-ttu-id="7b598-132">6\.</span><span class="sxs-lookup"><span data-stu-id="7b598-132">6\.</span></span> <span data-ttu-id="7b598-133">**Ctrl**+**F5** キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-133">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7b598-134">以前のプレビュー リリースの ASP.NET Core Blazor (Preview 6 以前) 用に Blazor Visual Studio 拡張機能をインストールした場合は、その拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="7b598-134">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="7b598-135">Visual Studio でテンプレートを表示するには、コマンド シェルで Blazor テンプレートをインストールするだけで十分になりました。</span><span class="sxs-lookup"><span data-stu-id="7b598-135">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="7b598-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b598-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="7b598-137">1\.</span><span class="sxs-lookup"><span data-stu-id="7b598-137">1\.</span></span> <span data-ttu-id="7b598-138">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="7b598-138">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="7b598-139">2\.</span><span class="sxs-lookup"><span data-stu-id="7b598-139">2\.</span></span> <span data-ttu-id="7b598-140">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b598-140">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

   <span data-ttu-id="7b598-141">3\.</span><span class="sxs-lookup"><span data-stu-id="7b598-141">3\.</span></span> <span data-ttu-id="7b598-142">Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-142">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="7b598-143">Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-143">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="7b598-144">2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b598-144">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="7b598-145">4\.</span><span class="sxs-lookup"><span data-stu-id="7b598-145">4\.</span></span> <span data-ttu-id="7b598-146">Visual Studio Code で *WebApplication1* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b598-146">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="7b598-147">5\.</span><span class="sxs-lookup"><span data-stu-id="7b598-147">5\.</span></span> <span data-ttu-id="7b598-148">Blazor Server プロジェクトについては、プロジェクトをビルドおよびデバッグするためにアセットを追加するように、IDE によって要求されます。</span><span class="sxs-lookup"><span data-stu-id="7b598-148">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="7b598-149">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-149">Select **Yes**.</span></span>

   <span data-ttu-id="7b598-150">6\.</span><span class="sxs-lookup"><span data-stu-id="7b598-150">6\.</span></span> <span data-ttu-id="7b598-151">Blazor Server アプリを使用する場合、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-151">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="7b598-152">Blazor WebAssembly アプリを使用する場合、アプリのプロジェクト フォルダーから `dotnet run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-152">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="7b598-153">7\.</span><span class="sxs-lookup"><span data-stu-id="7b598-153">7\.</span></span> <span data-ttu-id="7b598-154">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b598-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7b598-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7b598-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="7b598-156">1\.</span><span class="sxs-lookup"><span data-stu-id="7b598-156">1\.</span></span> <span data-ttu-id="7b598-157">[Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="7b598-157">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="7b598-158">2\.</span><span class="sxs-lookup"><span data-stu-id="7b598-158">2\.</span></span> <span data-ttu-id="7b598-159">**[ファイル]**  >  **[新しいソリューション]** を選択するか、**新しいプロジェクト**を作成します。</span><span class="sxs-lookup"><span data-stu-id="7b598-159">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="7b598-160">3\.</span><span class="sxs-lookup"><span data-stu-id="7b598-160">3\.</span></span> <span data-ttu-id="7b598-161">サイドバーで、 **[.NET Core]**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-161">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="7b598-162">4\.</span><span class="sxs-lookup"><span data-stu-id="7b598-162">4\.</span></span> <span data-ttu-id="7b598-163">**[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-163">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="7b598-164">現時点では、Visual Studio for Mac で使用できるのは Blazor Server テンプレートのみです。</span><span class="sxs-lookup"><span data-stu-id="7b598-164">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="7b598-165">Blazor WebAssembly については、 **[.NET Core CLI]** タブの指示に従ってください。Blazor Server テンプレートを選択したら、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-165">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="7b598-166">2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b598-166">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="7b598-167">5\.</span><span class="sxs-lookup"><span data-stu-id="7b598-167">5\.</span></span> <span data-ttu-id="7b598-168">**[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-168">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="7b598-169">6\.</span><span class="sxs-lookup"><span data-stu-id="7b598-169">6\.</span></span> <span data-ttu-id="7b598-170">**[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="7b598-170">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="7b598-171">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-171">Select **Create**.</span></span>

   <span data-ttu-id="7b598-172">7\.</span><span class="sxs-lookup"><span data-stu-id="7b598-172">7\.</span></span> <span data-ttu-id="7b598-173">*デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで実行する]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7b598-173">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="7b598-174">*デバッガーを使用して*アプリを実行するには、 **[デバッグの開始]** を選択してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-174">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="7b598-175">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-175">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="7b598-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7b598-176">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="7b598-177">Blazor WebAssembly エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-177">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="7b598-178">Blazor Server エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-178">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="7b598-179">2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b598-179">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="7b598-180">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b598-180">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="7b598-181">サイドバーのタブから複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b598-181">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="7b598-182">Home</span><span class="sxs-lookup"><span data-stu-id="7b598-182">Home</span></span>
* <span data-ttu-id="7b598-183">カウンター</span><span class="sxs-lookup"><span data-stu-id="7b598-183">Counter</span></span>
* <span data-ttu-id="7b598-184">Fetch data (データのフェッチ)</span><span class="sxs-lookup"><span data-stu-id="7b598-184">Fetch data</span></span>

<span data-ttu-id="7b598-185">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="7b598-185">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="7b598-186">Web ページでカウンターをインクリメントするには、通常、JavaScript を記述することが必要ですが、Blazor では C# を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b598-186">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="7b598-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="7b598-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="7b598-188">ブラウザーで `/counter` の要求があると、上部の `@page` ディレクティブで指定されているように、`Counter` コンポーネントによってそのコンテンツがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7b598-188">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="7b598-189">コンポーネントは、レンダリング ツリーのメモリ内表現としてレンダリングされ、柔軟かつ効率的な方法で UI を更新するために利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7b598-189">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="7b598-190">**[クリックしてください]** ボタンを選択するたびに:</span><span class="sxs-lookup"><span data-stu-id="7b598-190">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="7b598-191">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="7b598-191">The `onclick` event is fired.</span></span>
* <span data-ttu-id="7b598-192">`IncrementCount` メソッドが呼び出された場合。</span><span class="sxs-lookup"><span data-stu-id="7b598-192">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="7b598-193">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="7b598-193">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="7b598-194">コンポーネントが再度レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7b598-194">The component is rendered again.</span></span>

<span data-ttu-id="7b598-195">ランタイムで新しいコンテンツと前のコンテンツが比較され、変更されたコンテンツのみがドキュメント オブジェクト モデル (DOM) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="7b598-195">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="7b598-196">HTML 構文を使用して、別のコンポーネントにコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="7b598-196">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="7b598-197">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加することで、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="7b598-197">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="7b598-198">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="7b598-198">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="7b598-199">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-199">Run the app.</span></span> <span data-ttu-id="7b598-200">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="7b598-200">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="7b598-201">コンポーネント パラメーターは、属性または [子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="7b598-201">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="7b598-202">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="7b598-202">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="7b598-203">属性 `[Parameter]` を使用して、`IncrementAmount` のためのパブリック プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="7b598-203">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="7b598-204">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="7b598-204">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="7b598-205">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="7b598-205">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="7b598-206">属性を使用して、`Index` コンポーネントの `<Counter>` 要素内に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="7b598-206">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="7b598-207">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="7b598-207">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="7b598-208">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b598-208">Run the app.</span></span> <span data-ttu-id="7b598-209">`Index` コンポーネントは、 **[クリックしてください]** ボタンが選択されるたびに 10 ずつインクリメントされる独自のカウンターを持っています。</span><span class="sxs-lookup"><span data-stu-id="7b598-209">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="7b598-210">`/counter` にある `Counter` コンポーネント (*Counter.razor*) は、引き続き 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="7b598-210">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7b598-211">次の手順</span><span class="sxs-lookup"><span data-stu-id="7b598-211">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="7b598-212">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b598-212">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
