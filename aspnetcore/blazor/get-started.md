---
title: ASP.NET Core Blazor を使ってみる
author: guardrex
description: 好みのツールで Blazor アプリを構築して、Blazor の使用を開始しましょう。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: blazor/get-started
ms.openlocfilehash: b4609858be43acf9d1b2d8be5eff4879fd56f49f
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68948332"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="ac7ea-103">ASP.NET Core Blazor を使ってみる</span><span class="sxs-lookup"><span data-stu-id="ac7ea-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="ac7ea-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ac7ea-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ac7ea-105">Blazor を使ってみる:</span><span class="sxs-lookup"><span data-stu-id="ac7ea-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="ac7ea-106">最新の[.Net Core 3.0 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)リリースをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="ac7ea-107">コマンドシェルで次のコマンドを実行して、Blazor テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview7.19365.7
   ```

1. <span data-ttu-id="ac7ea-108">ツールの選択に関するガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac7ea-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac7ea-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="ac7ea-110">1 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-110">1\.</span></span> <span data-ttu-id="ac7ea-111">**ASP.NET と web 開発**ワークロードを使用して、最新の[Visual Studio preview](https://visualstudio.com/vs/preview)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ac7ea-112">2 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-112">2\.</span></span> <span data-ttu-id="ac7ea-113">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-113">Create a new project.</span></span>

   <span data-ttu-id="ac7ea-114">3 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-114">3\.</span></span> <span data-ttu-id="ac7ea-115">**[Blazor App]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-115">Select **Blazor App**.</span></span> <span data-ttu-id="ac7ea-116">          **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-116">Select **Next**.</span></span>

   <span data-ttu-id="ac7ea-117">4 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-117">4\.</span></span> <span data-ttu-id="ac7ea-118">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ac7ea-119">**場所**エントリが正しいことを確認するか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ac7ea-120">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-120">Select **Create**.</span></span>

   <span data-ttu-id="ac7ea-121">5 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-121">5\.</span></span> <span data-ttu-id="ac7ea-122">Blazor クライアント側のエクスペリエンスについては、 **Blazor (クライアント側)** テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-122">For a Blazor client-side experience, choose the **Blazor (client-side)** template.</span></span> <span data-ttu-id="ac7ea-123">Blazor サーバー側のエクスペリエンスについては、 **Blazor Server アプリ**テンプレートを選択してください。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-123">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ac7ea-124">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-124">Select **Create**.</span></span> <span data-ttu-id="ac7ea-125">サーバー側とクライアント側の2つの Blazor ホスティングモデルの詳細については<xref:blazor/hosting-models>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-125">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ac7ea-126">6 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-126">6\.</span></span> <span data-ttu-id="ac7ea-127">**F5 キー**を押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ac7ea-128">以前のプレビューリリースの ASP.NET Core Blazor (Preview 6 以前) 用に Blazor Visual Studio 拡張機能をインストールした場合は、プレビュー7で拡張機能をアンインストールできます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension at Preview 7.</span></span> <span data-ttu-id="ac7ea-129">Visual Studio でテンプレートを表示するには、コマンドシェルに Blazor テンプレートをインストールするだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac7ea-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac7ea-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="ac7ea-131">1 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-131">1\.</span></span> <span data-ttu-id="ac7ea-132">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="ac7ea-133">2 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-133">2\.</span></span> <span data-ttu-id="ac7ea-134">[ C# Visual Studio Code 拡張機能の](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="ac7ea-135">3 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-135">3\.</span></span> <span data-ttu-id="ac7ea-136">Blazor のクライアント側のエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-136">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="ac7ea-137">Blazor のサーバー側のエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-137">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      <span data-ttu-id="ac7ea-138">サーバー側とクライアント側の2つの Blazor ホスティングモデルの詳細については<xref:blazor/hosting-models>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-138">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ac7ea-139">4 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-139">4\.</span></span> <span data-ttu-id="ac7ea-140">Visual Studio Code で*WebApplication1*フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="ac7ea-141">5 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-141">5\.</span></span> <span data-ttu-id="ac7ea-142">Blazor のサーバー側プロジェクトの場合、IDE は、プロジェクトをビルドおよびデバッグするためにアセットを追加するように要求します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-142">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="ac7ea-143">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-143">Select **Yes**.</span></span>

   <span data-ttu-id="ac7ea-144">6 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-144">6\.</span></span> <span data-ttu-id="ac7ea-145">Blazor サーバー側アプリを使用している場合は、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-145">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="ac7ea-146">Blazor クライアント側アプリを使用している場合`dotnet run`は、アプリのプロジェクトフォルダーからを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-146">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="ac7ea-147">7 \。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-147">7\.</span></span> <span data-ttu-id="ac7ea-148">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor Server App** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ac7ea-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ac7ea-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="ac7ea-150">Blazor のクライアント側のエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-150">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ac7ea-151">Blazor のサーバー側のエクスペリエンスについては、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-151">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ac7ea-152">サーバー側とクライアント側の2つの Blazor ホスティングモデルの詳細については<xref:blazor/hosting-models>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-152">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ac7ea-153">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="ac7ea-154">サイドバーのタブからは、複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="ac7ea-155">ホーム (Home)</span><span class="sxs-lookup"><span data-stu-id="ac7ea-155">Home</span></span>
* <span data-ttu-id="ac7ea-156">カウンター</span><span class="sxs-lookup"><span data-stu-id="ac7ea-156">Counter</span></span>
* <span data-ttu-id="ac7ea-157">データのフェッチ</span><span class="sxs-lookup"><span data-stu-id="ac7ea-157">Fetch data</span></span>

<span data-ttu-id="ac7ea-158">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ac7ea-159">通常、web ページでカウンターを増やすには JavaScript を記述する必要がありますがC#、を使用して Razor コンポーネントの方が優れています。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="ac7ea-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ac7ea-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="ac7ea-161">ブラウザーでの`/counter`要求が、上部の`@page`ディレクティブで指定されている場合、コンポーネント`Counter`はそのコンテンツをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="ac7ea-162">コンポーネントは、レンダリングツリーのメモリ内表現にレンダリングされ、柔軟で効率的な方法で UI を更新するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="ac7ea-163">**[クリックし**てください] ボタンが選択されるたびに、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="ac7ea-164">`onclick`イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="ac7ea-165">`IncrementCount` メソッドが呼び出された場合。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="ac7ea-166">`currentCount`がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="ac7ea-167">コンポーネントが再び表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-167">The component is rendered again.</span></span>

<span data-ttu-id="ac7ea-168">ランタイムは、新しいコンテンツを前のコンテンツと比較し、変更されたコンテンツのみをドキュメントオブジェクトモデル (DOM) に適用します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="ac7ea-169">HTML 構文を使用してコンポーネントを別のコンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="ac7ea-170">たとえば、コンポーネントに`Counter` `<Counter />` `Index`要素を追加して、コンポーネントをアプリのホームページに追加します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="ac7ea-171">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ac7ea-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="ac7ea-172">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-172">Run the app.</span></span> <span data-ttu-id="ac7ea-173">ホームページには、 `Counter`コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="ac7ea-174">コンポーネントのパラメーターは、属性または[子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="ac7ea-175">`Counter`コンポーネントにパラメーターを追加するには、コンポーネントの`@code`ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="ac7ea-176">属性`[Parameter]`を使用し`IncrementAmount`て、のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-176">Add a property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="ac7ea-177">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="ac7ea-178">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ac7ea-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="ac7ea-179">属性を`IncrementAmount`使用し`Index`て、 `<Counter>`コンポーネントの要素でを指定します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="ac7ea-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ac7ea-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="ac7ea-181">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-181">Run the app.</span></span> <span data-ttu-id="ac7ea-182">コンポーネントには、 **[クリックし**てください] ボタンが選択されるたびに10ずつ増加する独自のカウンターがあります。 `Index`</span><span class="sxs-lookup"><span data-stu-id="ac7ea-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ac7ea-183">の`Counter`コンポーネント (*Counter*) `/counter`は、1つずつ増加し続けています。</span><span class="sxs-lookup"><span data-stu-id="ac7ea-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac7ea-184">次の手順</span><span class="sxs-lookup"><span data-stu-id="ac7ea-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="ac7ea-185">その他の資料</span><span class="sxs-lookup"><span data-stu-id="ac7ea-185">Additional resources</span></span>

* <xref:signalr/introduction>
