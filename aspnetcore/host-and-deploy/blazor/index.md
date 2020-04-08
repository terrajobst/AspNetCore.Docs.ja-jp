---
title: ASP.NET Core Blazor のホストと展開
author: guardrex
description: Blazor アプリをホストおよび展開する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434266"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="6c557-103">ASP.NET Core Blazor のホストと展開</span><span class="sxs-lookup"><span data-stu-id="6c557-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="6c557-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6c557-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="6c557-105">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="6c557-105">Publish the app</span></span>

<span data-ttu-id="6c557-106">アプリは、リリース構成での展開のために発行されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c557-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c557-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6c557-108">**[ビルド]**  >  **[Publish {APPLICATION}]\({アプリケーション} を発行する\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6c557-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="6c557-109">*[publish target]\(発行先\)* を選択します。</span><span class="sxs-lookup"><span data-stu-id="6c557-109">Select the *publish target*.</span></span> <span data-ttu-id="6c557-110">ローカルに発行するには、 **[フォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6c557-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="6c557-111">**[フォルダーの選択]** フィールド内で既定の場所を受け入れるか、または別の場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="6c557-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="6c557-112">**[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6c557-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6c557-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6c557-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6c557-114">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用して、リリース構成によってアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="6c557-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="6c557-115">アプリを発行すると、プロジェクトの依存関係の[復元](/dotnet/core/tools/dotnet-restore)がトリガーされ、展開されるアセットを作成する前にプロジェクトが[ビルド](/dotnet/core/tools/dotnet-build)されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="6c557-116">ビルド プロセスの一環として、アプリのダウンロード サイズを縮小し読み込み時間を短縮するため、未使用のメソッドおよびアセンブリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="6c557-117">発行場所:</span><span class="sxs-lookup"><span data-stu-id="6c557-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="6c557-118"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6c557-118"> WebAssembly</span></span>
  * <span data-ttu-id="6c557-119">スタンドアロン &ndash; アプリは " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="6c557-120">アプリを静的サイトとして展開するには、*wwwroot* フォルダーの内容を静的サイトのホストにコピーします。</span><span class="sxs-lookup"><span data-stu-id="6c557-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="6c557-121">ホステッド &ndash; クライアント Blazor WebAssembly アプリは、サーバ ーアプリの他の静的な Web アセットと共に、サーバー アプリの " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="6c557-122">*publish* フォルダーの内容をホストに展開します。</span><span class="sxs-lookup"><span data-stu-id="6c557-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="6c557-123"> サーバー &ndash; アプリは " */bin/Release/{ターゲット フレームワーク}/publish*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="6c557-124">*publish* フォルダーの内容をホストに展開します。</span><span class="sxs-lookup"><span data-stu-id="6c557-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="6c557-125">フォルダー内のアセットは、Web サーバーに展開されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="6c557-126">展開のプロセスが手動であるか自動であるかは、ご使用の展開ツールによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6c557-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="6c557-127">アプリのベース パス</span><span class="sxs-lookup"><span data-stu-id="6c557-127">App base path</span></span>

<span data-ttu-id="6c557-128">*アプリのベース パス*とは、アプリのルート URL パスのことです。</span><span class="sxs-lookup"><span data-stu-id="6c557-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="6c557-129">次の ASP.NET Core アプリと Blazor サブアプリについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6c557-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="6c557-130">ASP.NET Core アプリは `MyApp` と命名します。</span><span class="sxs-lookup"><span data-stu-id="6c557-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="6c557-131">このアプリは、物理的に *d:/MyApp* にあります。</span><span class="sxs-lookup"><span data-stu-id="6c557-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="6c557-132">要求は、`https://www.contoso.com/{MYAPP RESOURCE}` で受信されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="6c557-133">Blazor という名前の `CoolApp` アプリは `MyApp` のサブアプリです。</span><span class="sxs-lookup"><span data-stu-id="6c557-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="6c557-134">このサブアプリは、物理的に *d:/MyApp/CoolApp* にあります。</span><span class="sxs-lookup"><span data-stu-id="6c557-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="6c557-135">要求は、`https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` で受信されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="6c557-136">`CoolApp` に対して構成を追加指定しない場合、このシナリオではサブ アプリにはそれがサーバー上のどこの場所にあるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="6c557-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="6c557-137">たとえば、相対 URL パス `/CoolApp/` にあることがわからない場合、アプリはそのリソースに対する正しい相対 URL を作成できません。</span><span class="sxs-lookup"><span data-stu-id="6c557-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="6c557-138">Blazor タグの `https://www.contoso.com/CoolApp/` 属性は、`<base>` アプリのベース パスの `href` に構成を指定するため、*Pages/_Host.cshtml* ファイル (Blazor サーバー) または *wwwroot/index.html* ファイル (Blazor WebAssembly) の相対ルート パスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="6c557-139"> サーバー アプリはさらに、<xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> のアプリの要求パイプラインで `Startup.Configure` を呼び出すことによって、サーバー側のベース パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6c557-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="6c557-140">相対 URL を指定することにより、ルート ディレクトリに存在しないコンポーネントでアプリのルート パスへの相対 URL を構築できます。</span><span class="sxs-lookup"><span data-stu-id="6c557-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="6c557-141">ディレクトリ構造の別のレベルに存在するコンポーネントが、アプリ内のさまざまな場所にある他のリソースに対するリンクを構築できます。</span><span class="sxs-lookup"><span data-stu-id="6c557-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="6c557-142">アプリのベース パスは、リンクの `href` ターゲットがアプリのベース パス URI 空間内にある、選択されたハイパーリンクの阻止にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="6c557-143">内部のナビゲーションは、Blazor ルーターによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6c557-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="6c557-144">多くのホスティング シナリオでは、アプリへの相対 URL パスは、アプリのルートです。</span><span class="sxs-lookup"><span data-stu-id="6c557-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="6c557-145">これらの場合、アプリの相対 URL ベース パスにフォワード スラッシュ (`<base href="/" />`) が付きます。これは、Blazor アプリの既定の構成です。</span><span class="sxs-lookup"><span data-stu-id="6c557-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="6c557-146">GitHub ページと IIS サブアプリなど、その他のホスティング シナリオの場合、アプリのベースパスは、アプリへのサーバーの相対 URL パスに設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6c557-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="6c557-147">アプリのベース パスを設定するには、`<base>`Pages/_Host.cshtml`<head>` ファイル (*サーバー) または*wwwroot/index.htmlBlazor ファイル (*WebAssembly) の* タグ要素内の Blazor を更新します。</span><span class="sxs-lookup"><span data-stu-id="6c557-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="6c557-148">`href` 属性値を `/{RELATIVE URL PATH}/` (末尾にスラッシュが必要) に設定します。ここで、`{RELATIVE URL PATH}` は、アプリの完全な相対 URL パスです。</span><span class="sxs-lookup"><span data-stu-id="6c557-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="6c557-149">ルート以外の相対 URL パスが構成されている Blazor WebAssembly アプリの場合 (例: `<base href="/CoolApp/">`)、そのアプリは*ローカルで実行*されると自身のリソースを見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="6c557-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="6c557-150">ローカルでの開発およびテスト中は、実行時の *タグの* 値と一致する`href`パス ベース`<base>`引数を指定することで、この問題を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="6c557-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="6c557-151">末尾にはスラッシュを含めないでください。</span><span class="sxs-lookup"><span data-stu-id="6c557-151">Don't include a trailing slash.</span></span> <span data-ttu-id="6c557-152">アプリをローカルで実行しているときにパス ベースの引数を渡すには、アプリのディレクトリから `dotnet run` オプションを指定して `--pathbase` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6c557-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="6c557-153">相対 URL パスが Blazor (`/CoolApp/`) の `<base href="/CoolApp/">` WebAssembly アプリについては、このコマンドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6c557-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="6c557-154">Blazor WebAssembly アプリは `http://localhost:port/CoolApp` でローカルで応答します。</span><span class="sxs-lookup"><span data-stu-id="6c557-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="6c557-155">配置</span><span class="sxs-lookup"><span data-stu-id="6c557-155">Deployment</span></span>

<span data-ttu-id="6c557-156">展開のガイダンスについては、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c557-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
