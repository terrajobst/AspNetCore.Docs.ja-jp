---
title: JavaScript サービスを使用して ASP.NET Core にシングルページアプリケーションを作成する
author: scottaddie
description: JavaScript サービスを使用して、ASP.NET Core によってサポートされるシングルページアプリケーション (SPA) を作成する利点について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: 52285999d7710cc3198836b9246596980cfc1666
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355785"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="2d339-103">JavaScript サービスを使用して ASP.NET Core にシングルページアプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="2d339-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="2d339-104">によって[Scott Addie](https://github.com/scottaddie)と[Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="2d339-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="2d339-105">シングル ページ アプリケーション (SPA) は、その固有の機能豊富なユーザー エクスペリエンスのための web アプリケーションの人気のある型です。</span><span class="sxs-lookup"><span data-stu-id="2d339-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="2d339-106">クライアント側の SPA フレームワークまたはライブラリ ([角度](https://angular.io/)や[反応](https://facebook.github.io/react/)など) をサーバー側のフレームワーク (ASP.NET Core など) と統合することは困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="2d339-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="2d339-107">JavaScript サービスは、統合プロセスの摩擦を減らすために開発されました。</span><span class="sxs-lookup"><span data-stu-id="2d339-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="2d339-108">これにより、別のクライアントおよびサーバー テクノロジ スタックとの間のシームレスな操作ができます。</span><span class="sxs-lookup"><span data-stu-id="2d339-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="2d339-109">この記事で説明されている機能は、ASP.NET Core 3.0 の場合は廃止されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2d339-110">[Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet パッケージでは、より単純な SPA フレームワーク統合メカニズムを使用できます。</span><span class="sxs-lookup"><span data-stu-id="2d339-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="2d339-111">詳細については、「 [[アナウンスメント] Obsoleting AspNetCore. SpaServices And AspNetCore. NodeServices](https://github.com/aspnet/AspNetCore/issues/12890)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d339-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/aspnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="2d339-112">JavaScript サービスとは</span><span class="sxs-lookup"><span data-stu-id="2d339-112">What is JavaScript Services</span></span>

<span data-ttu-id="2d339-113">JavaScript サービスは、ASP.NET Core のクライアント側テクノロジのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="2d339-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="2d339-114">その目的は、Spa を構築するための開発者の推奨されるサーバー側プラットフォームとしての ASP.NET Core の位置です。</span><span class="sxs-lookup"><span data-stu-id="2d339-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="2d339-115">JavaScript サービスは、次の2つの異なる NuGet パッケージで構成されています。</span><span class="sxs-lookup"><span data-stu-id="2d339-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="2d339-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="2d339-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="2d339-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="2d339-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="2d339-118">これらのパッケージは、次のシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2d339-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="2d339-119">サーバーでの JavaScript を実行します。</span><span class="sxs-lookup"><span data-stu-id="2d339-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="2d339-120">SPA フレームワークやライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="2d339-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="2d339-121">Webpack とクライアント側アセットをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2d339-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="2d339-122">SpaServices パッケージを使用してこの記事内のフォーカスの多くは配置されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="2d339-123">SpaServices とは</span><span class="sxs-lookup"><span data-stu-id="2d339-123">What is SpaServices</span></span>

<span data-ttu-id="2d339-124">SpaServices は、Spa を構築するための開発者の推奨されるサーバー側プラットフォームとしての ASP.NET Core の位置に作成されました。</span><span class="sxs-lookup"><span data-stu-id="2d339-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="2d339-125">SpaServices は、ASP.NET Core で SPAs を開発するためには必要ありません。また、開発者は特定のクライアントフレームワークにロックされません。</span><span class="sxs-lookup"><span data-stu-id="2d339-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="2d339-126">SpaServices は、次のような便利なインフラストラクチャを提供します。</span><span class="sxs-lookup"><span data-stu-id="2d339-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="2d339-127">サーバー側の事前</span><span class="sxs-lookup"><span data-stu-id="2d339-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="2d339-128">Webpack 開発ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="2d339-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="2d339-129">ホットなモジュールの交換</span><span class="sxs-lookup"><span data-stu-id="2d339-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="2d339-130">ルーティングのヘルパー</span><span class="sxs-lookup"><span data-stu-id="2d339-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="2d339-131">総称して、これらのインフラストラクチャ コンポーネントは、開発ワークフローと、実行時のエクスペリエンスの両方を強化します。</span><span class="sxs-lookup"><span data-stu-id="2d339-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="2d339-132">コンポーネントを個別に採用することができます。</span><span class="sxs-lookup"><span data-stu-id="2d339-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="2d339-133">SpaServices を使用するための前提条件</span><span class="sxs-lookup"><span data-stu-id="2d339-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="2d339-134">SpaServices を使用するには、次のようにインストールします。</span><span class="sxs-lookup"><span data-stu-id="2d339-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="2d339-135">[Node.js](https://nodejs.org/) (バージョン 6 以降) で npm</span><span class="sxs-lookup"><span data-stu-id="2d339-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="2d339-136">これらのコンポーネントがインストールされを検出できることを確認するには、コマンドラインから、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="2d339-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="2d339-137">Azure の web サイトにデプロイする場合、操作は必要ありません&mdash;node.js がインストールされ、サーバー環境で使用できます。</span><span class="sxs-lookup"><span data-stu-id="2d339-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="2d339-138">Visual Studio 2017 を使用している Windows では、 **.Net Core クロスプラットフォーム開発**ワークロードを選択して SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2d339-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="2d339-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="2d339-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="2d339-140">サーバー側の事前</span><span class="sxs-lookup"><span data-stu-id="2d339-140">Server-side prerendering</span></span>

<span data-ttu-id="2d339-141">ユニバーサル (isomorphic とも呼ばれます) のアプリケーションは、サーバーとクライアントの両方で実行できる JavaScript アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="2d339-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="2d339-142">Angular、React、およびその他の一般的なフレームワークは、このアプリケーションの開発スタイルのユニバーサル プラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="2d339-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="2d339-143">考え方は、まず、Node.js を使用して、サーバー上のフレームワーク コンポーネントをレンダリングし、さらに、クライアントを実行しを委任します。</span><span class="sxs-lookup"><span data-stu-id="2d339-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="2d339-144">ASP.NET Core[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)によって提供される SpaServices、サーバー上の JavaScript 関数を呼び出すことによってサーバー側の事前の実装を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="2d339-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="2d339-145">サーバー側のプリレンダリングの前提条件</span><span class="sxs-lookup"><span data-stu-id="2d339-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="2d339-146">[Aspnet プリレンダリング](https://www.npmjs.com/package/aspnet-prerendering)npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2d339-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="2d339-147">サーバー側のプリレンダリング構成</span><span class="sxs-lookup"><span data-stu-id="2d339-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="2d339-148">タグ ヘルパーは、プロジェクトの名前空間の登録を使用して探索可能にされて *_ViewImports.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="2d339-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="2d339-149">これらのタグ ヘルパーは Razor ビュー内の HTML のような構文を活用することで、低レベルの Api と直接通信の複雑さで抽象化します。</span><span class="sxs-lookup"><span data-stu-id="2d339-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="2d339-150">asp-prerender-module タグヘルパー</span><span class="sxs-lookup"><span data-stu-id="2d339-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="2d339-151">`asp-prerender-module`タグ ヘルパーの前のコード例で使用される実行*ClientApp/dist/main-server.js* Node.js を使用してサーバーにします。</span><span class="sxs-lookup"><span data-stu-id="2d339-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="2d339-152">わかりやすくするためのために、 *main server.js*ファイルは、TypeScript、JavaScript にトランス パイルもタスクの成果物、 [Webpack](https://webpack.github.io/)プロセスを構築します。</span><span class="sxs-lookup"><span data-stu-id="2d339-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="2d339-153">Webpack のエントリ ポイントのエイリアスを定義します`main-server`; と、このエイリアスの依存関係グラフのトラバーサルが始まり、 *ClientApp/ブート-server.ts*ファイル。</span><span class="sxs-lookup"><span data-stu-id="2d339-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="2d339-154">次の Angular の例では、 *ClientApp/ブート-server.ts*ファイルを利用、`createServerRenderer`関数と`RenderResult`の入力、 `aspnet-prerendering` npm パッケージを Node.js を使用してサーバー レンダリングを構成します。</span><span class="sxs-lookup"><span data-stu-id="2d339-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="2d339-155">サーバー側のレンダリングが厳密に型指定された JavaScript にラップされて解決関数の呼び出しに渡される宛ての HTML マークアップ`Promise`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="2d339-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="2d339-156">`Promise`オブジェクトの重要性は、DOM のプレース ホルダー要素の挿入のページに HTML マークアップを非同期的に提供します。</span><span class="sxs-lookup"><span data-stu-id="2d339-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="2d339-157">asp-prerender-データタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="2d339-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="2d339-158">組み合わせると、`asp-prerender-module`タグ ヘルパーの`asp-prerender-data`タグ ヘルパーは、Razor ビューからサーバー側 JavaScript にコンテキスト情報を渡すために使用できます。</span><span class="sxs-lookup"><span data-stu-id="2d339-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="2d339-159">たとえば、次のマークアップは合格ユーザー データを`main-server`モジュール。</span><span class="sxs-lookup"><span data-stu-id="2d339-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="2d339-160">受信した`UserName`引数の組み込みの JSON シリアライザーを使用してシリアル化し、は、`params.data`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="2d339-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="2d339-161">次の例で Angular 内でパーソナライズされたあいさつ文を構築するデータの使用、`h1`要素。</span><span class="sxs-lookup"><span data-stu-id="2d339-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="2d339-162">タグヘルパーで渡されるプロパティ名は、文字表記の**case**表記で表されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="2d339-163">同じプロパティ名で表現は JavaScript とは異なり**camelCase**します。</span><span class="sxs-lookup"><span data-stu-id="2d339-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="2d339-164">既定の JSON シリアル化の構成では、この違いを担当します。</span><span class="sxs-lookup"><span data-stu-id="2d339-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="2d339-165">展開すると、上記のコード例に、データ渡し可能サーバーからビューに hydrating、`globals`プロパティに提供される、`resolve`関数。</span><span class="sxs-lookup"><span data-stu-id="2d339-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="2d339-166">`postList`配列内で定義されている、`globals`オブジェクトはグローバルで、ブラウザーにアタッチされて`window`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="2d339-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="2d339-167">グローバル スコープにこの変数のホイストでは、特に、サーバーで 1 回と、クライアントでは、同じデータの読み込みに関連している、作業の重複がなくなります。</span><span class="sxs-lookup"><span data-stu-id="2d339-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![ウィンドウ オブジェクトにアタッチされているグローバルの postList 変数](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="2d339-169">Webpack 開発ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="2d339-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="2d339-170">[Webpack 開発ミドルウェア](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)Webpack がオンデマンドでリソースをビルドするための合理的な開発ワークフローが導入されています。</span><span class="sxs-lookup"><span data-stu-id="2d339-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="2d339-171">ミドルウェアが自動的にコンパイルし、ページがブラウザーに再読み込みする際、クライアント側のリソースの機能します。</span><span class="sxs-lookup"><span data-stu-id="2d339-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="2d339-172">別の方法では、サードパーティの依存関係またはカスタム コードが変更されたときに、プロジェクトの npm ビルド スクリプトを使用して Webpack を手動で起動します。</span><span class="sxs-lookup"><span data-stu-id="2d339-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="2d339-173">Npm スクリプトを作成する、 *package.json*ファイルは、次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="2d339-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="2d339-174">Webpack Dev ミドルウェアの前提条件</span><span class="sxs-lookup"><span data-stu-id="2d339-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="2d339-175">[Aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2d339-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="2d339-176">Webpack 開発ミドルウェアの構成</span><span class="sxs-lookup"><span data-stu-id="2d339-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="2d339-177">次のコードを使用して HTTP 要求パイプラインに Webpack 開発ミドルウェアが登録されている、 *Startup.cs*ファイルの`Configure`メソッド。</span><span class="sxs-lookup"><span data-stu-id="2d339-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="2d339-178">`UseWebpackDevMiddleware`する前に、拡張メソッドを呼び出す必要があります[静的ファイルをホストしている登録](xref:fundamentals/static-files)を使用して、`UseStaticFiles`拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="2d339-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="2d339-179">セキュリティ上の理由から、アプリは開発モードで実行時にのみ、ミドルウェアを登録します。</span><span class="sxs-lookup"><span data-stu-id="2d339-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="2d339-180">*Webpack.config.js*ファイルの`output.publicPath`プロパティに通知を監視するミドルウェア、`dist`フォルダーの変更。</span><span class="sxs-lookup"><span data-stu-id="2d339-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="2d339-181">ホットなモジュールの交換</span><span class="sxs-lookup"><span data-stu-id="2d339-181">Hot Module Replacement</span></span>

<span data-ttu-id="2d339-182">Webpack の考える[ホット モジュールの交換](https://webpack.js.org/concepts/hot-module-replacement/)(HMR) 機能の進化したものとして[Webpack 開発ミドルウェア](#webpack-dev-middleware)します。</span><span class="sxs-lookup"><span data-stu-id="2d339-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="2d339-183">すべて同じメリットが導入されて HMR が自動的に変更をコンパイルした後、ページのコンテンツを更新することでさらに、開発ワークフローを効率化します。</span><span class="sxs-lookup"><span data-stu-id="2d339-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="2d339-184">メモリ内の現在の状態と、SPA のデバッグ セッションに支障をきたすのブラウザーの更新でこれを混同しないでください。</span><span class="sxs-lookup"><span data-stu-id="2d339-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="2d339-185">Webpack 開発ミドルウェア サービスと、ブラウザーに変更がプッシュされることを意味すると、ブラウザーの間のライブ リンクがあります。</span><span class="sxs-lookup"><span data-stu-id="2d339-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="2d339-186">ホットモジュール置換の前提条件</span><span class="sxs-lookup"><span data-stu-id="2d339-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="2d339-187">[Webpack-hot ミドルウェア](https://www.npmjs.com/package/webpack-hot-middleware)npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2d339-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="2d339-188">ホットモジュールの交換の構成</span><span class="sxs-lookup"><span data-stu-id="2d339-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="2d339-189">MVC の HTTP 要求パイプラインに HMR コンポーネントを登録する必要があります、`Configure`メソッド。</span><span class="sxs-lookup"><span data-stu-id="2d339-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="2d339-190">そうであったとして[Webpack 開発ミドルウェア](#webpack-dev-middleware)、`UseWebpackDevMiddleware`する前に、拡張メソッドを呼び出す必要があります、`UseStaticFiles`拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="2d339-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="2d339-191">セキュリティ上の理由から、アプリは開発モードで実行時にのみ、ミドルウェアを登録します。</span><span class="sxs-lookup"><span data-stu-id="2d339-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="2d339-192">*Webpack.config.js*ファイルを定義する必要があります、`plugins`場合でも、それを空の配列。</span><span class="sxs-lookup"><span data-stu-id="2d339-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="2d339-193">ブラウザーでアプリを読み込んだ後は、開発者ツールのコンソール タブは、HMR アクティブ化の確認を提供します。</span><span class="sxs-lookup"><span data-stu-id="2d339-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![ホットのモジュールの交換接続メッセージ](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="2d339-195">ルーティングのヘルパー</span><span class="sxs-lookup"><span data-stu-id="2d339-195">Routing helpers</span></span>

<span data-ttu-id="2d339-196">ほとんどの ASP.NET Core ベースの SPAs では、サーバー側のルーティングに加えて、クライアント側のルーティングが必要になることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2d339-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="2d339-197">SPA と MVC ルーティング システムは、競合することがなく個別に作業できます。</span><span class="sxs-lookup"><span data-stu-id="2d339-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="2d339-198">ただし、課題を 1 つエッジ ケース読んだり: 404 の HTTP 応答を識別します。</span><span class="sxs-lookup"><span data-stu-id="2d339-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="2d339-199">シナリオの場合を検討してください、拡張子のないルートの`/some/page`使用されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="2d339-200">要求がパターン一致、サーバー側ルートがそのパターンのクライアント側ルートが一致するものとします。</span><span class="sxs-lookup"><span data-stu-id="2d339-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="2d339-201">ここでの受信要求を考えてみます`/images/user-512.png`、一般に、サーバー上の画像ファイルを検索する要求。</span><span class="sxs-lookup"><span data-stu-id="2d339-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="2d339-202">要求されたリソースパスがどのサーバー側ルートまたは静的ファイルとも一致しない場合、通常は 404 HTTP 状態コードを返す&mdash;クライアント側アプリケーションがそれを処理することはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="2d339-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="2d339-203">ルーティングヘルパーの前提条件</span><span class="sxs-lookup"><span data-stu-id="2d339-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="2d339-204">クライアント側ルーティングの npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2d339-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="2d339-205">Angular を使用して、例として。</span><span class="sxs-lookup"><span data-stu-id="2d339-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="2d339-206">ルーティングヘルパーの構成</span><span class="sxs-lookup"><span data-stu-id="2d339-206">Routing helpers configuration</span></span>

<span data-ttu-id="2d339-207">という名前の拡張メソッド`MapSpaFallbackRoute`で使用されて、`Configure`メソッド。</span><span class="sxs-lookup"><span data-stu-id="2d339-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="2d339-208">ルートは、構成されている順序で評価されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="2d339-209">その結果、`default`パターン一致の前のコード例ではルートが最初に使用します。</span><span class="sxs-lookup"><span data-stu-id="2d339-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="2d339-210">新しいプロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="2d339-210">Create a new project</span></span>

<span data-ttu-id="2d339-211">JavaScript サービスには、構成済みのアプリケーションテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2d339-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="2d339-212">SpaServices は、これらのテンプレートで、角度、反応、Redux などのさまざまなフレームワークやライブラリと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="2d339-213">これらのテンプレートは、次のコマンドを実行して .NET Core CLI を使用してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="2d339-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="2d339-214">使用可能な SPA テンプレートの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="2d339-215">テンプレート</span><span class="sxs-lookup"><span data-stu-id="2d339-215">Templates</span></span>                                 | <span data-ttu-id="2d339-216">短い形式の名前</span><span class="sxs-lookup"><span data-stu-id="2d339-216">Short Name</span></span> | <span data-ttu-id="2d339-217">言語</span><span class="sxs-lookup"><span data-stu-id="2d339-217">Language</span></span> | <span data-ttu-id="2d339-218">Tags</span><span class="sxs-lookup"><span data-stu-id="2d339-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="2d339-219">Angular と ASP.NET Core の MVC</span><span class="sxs-lookup"><span data-stu-id="2d339-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="2d339-220">angular</span><span class="sxs-lookup"><span data-stu-id="2d339-220">angular</span></span>    | <span data-ttu-id="2d339-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="2d339-221">[C#]</span></span>     | <span data-ttu-id="2d339-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="2d339-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="2d339-223">React.js 付きの ASP.NET Core の MVC</span><span class="sxs-lookup"><span data-stu-id="2d339-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="2d339-224">react</span><span class="sxs-lookup"><span data-stu-id="2d339-224">react</span></span>      | <span data-ttu-id="2d339-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="2d339-225">[C#]</span></span>     | <span data-ttu-id="2d339-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="2d339-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="2d339-227">MVC の ASP.NET Core react.js と Redux</span><span class="sxs-lookup"><span data-stu-id="2d339-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="2d339-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="2d339-228">reactredux</span></span> | <span data-ttu-id="2d339-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="2d339-229">[C#]</span></span>     | <span data-ttu-id="2d339-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="2d339-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="2d339-231">SPA テンプレートのいずれかを使用して新しいプロジェクトを作成するには含める、**短い名前**内のテンプレートの[新しい dotnet](/dotnet/core/tools/dotnet-new)コマンド。</span><span class="sxs-lookup"><span data-stu-id="2d339-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="2d339-232">次のコマンドでは、サーバー側用に構成された ASP.NET Core MVC で Angular アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="2d339-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="2d339-233">ランタイムの構成モードを設定します。</span><span class="sxs-lookup"><span data-stu-id="2d339-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="2d339-234">2 つのプライマリのランタイム構成モードは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2d339-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="2d339-235">**開発**:</span><span class="sxs-lookup"><span data-stu-id="2d339-235">**Development**:</span></span>
  * <span data-ttu-id="2d339-236">デバッグを容易にするソース マップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2d339-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="2d339-237">パフォーマンスのクライアント側のコードを最適化しません。</span><span class="sxs-lookup"><span data-stu-id="2d339-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="2d339-238">**実稼働**:</span><span class="sxs-lookup"><span data-stu-id="2d339-238">**Production**:</span></span>
  * <span data-ttu-id="2d339-239">ソース マップを除外します。</span><span class="sxs-lookup"><span data-stu-id="2d339-239">Excludes source maps.</span></span>
  * <span data-ttu-id="2d339-240">バンドルと縮小を使用して、クライアント側のコードを最適化します。</span><span class="sxs-lookup"><span data-stu-id="2d339-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="2d339-241">ASP.NET Core という環境変数を使用して`ASPNETCORE_ENVIRONMENT`構成モードを格納します。</span><span class="sxs-lookup"><span data-stu-id="2d339-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="2d339-242">詳細については、「[環境を設定する](xref:fundamentals/environments#set-the-environment)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d339-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="2d339-243">.NET Core CLI で実行する</span><span class="sxs-lookup"><span data-stu-id="2d339-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="2d339-244">プロジェクトのルートで、次のコマンドを実行してには、必要な NuGet と npm パッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="2d339-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="2d339-245">ビルドし、アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d339-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="2d339-246">アプリケーションの起動時に従い、localhost 上で、[ランタイム構成モード](#set-the-runtime-configuration-mode)します。</span><span class="sxs-lookup"><span data-stu-id="2d339-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="2d339-247">移動する`http://localhost:5000`ブラウザーにランディング ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="2d339-248">Visual Studio 2017 で実行する</span><span class="sxs-lookup"><span data-stu-id="2d339-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="2d339-249">開く、 *.csproj*によって生成されたファイル、[新しい dotnet](/dotnet/core/tools/dotnet-new)コマンド。</span><span class="sxs-lookup"><span data-stu-id="2d339-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="2d339-250">必要な NuGet と npm パッケージは、プロジェクトを開くと自動的に復元されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="2d339-251">この復元プロセスは、数分かかる場合があり、アプリケーションが完了するときに実行する準備ができます。</span><span class="sxs-lookup"><span data-stu-id="2d339-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="2d339-252">キーを押して、緑色の実行 ボタンをクリックします`Ctrl + F5`、し、アプリケーションのランディング ページで、ブラウザーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="2d339-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="2d339-253">アプリケーションによる localhost で実行、[ランタイム構成モード](#set-the-runtime-configuration-mode)します。</span><span class="sxs-lookup"><span data-stu-id="2d339-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="2d339-254">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2d339-254">Test the app</span></span>

<span data-ttu-id="2d339-255">SpaServices テンプレートは、事前構成を使用してクライアント側のテストを実行して[Karma](https://karma-runner.github.io/1.0/index.html)と[Jasmine](https://jasmine.github.io/)します。</span><span class="sxs-lookup"><span data-stu-id="2d339-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="2d339-256">Jasmine は、Karma テスト ランナーはこれらのテストには、人気のある単位の JavaScript 用のテスト フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="2d339-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="2d339-257">Karma を使用するように構成、 [Webpack 開発ミドルウェア](#webpack-dev-middleware)されるよう、開発者が停止し、変更されるたびにテストを実行するために必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2d339-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="2d339-258">テスト_ケースまたはテスト ケース自体に対して実行されるコードであるかどうか、テストが自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="2d339-259">Angular アプリケーションを使用して、例として、2 つの Jasmine テスト_ケースが既に提供されている、`CounterComponent`で、 *counter.component.spec.ts*ファイル。</span><span class="sxs-lookup"><span data-stu-id="2d339-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="2d339-260">コマンド プロンプトを開き、 *ClientApp*ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="2d339-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="2d339-261">コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d339-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="2d339-262">スクリプトで定義されている設定を読み取り、Karma テスト ランナーを起動する、 *karma.conf.js*ファイル。</span><span class="sxs-lookup"><span data-stu-id="2d339-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="2d339-263">その他の設定の間で、 *karma.conf.js*経由で実行するテスト ファイルを識別します。 その`files`配列。</span><span class="sxs-lookup"><span data-stu-id="2d339-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="2d339-264">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="2d339-264">Publish the app</span></span>

<span data-ttu-id="2d339-265">Azure への発行の詳細については、こちらの[GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/12474)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d339-265">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="2d339-266">デプロイの準備完了パッケージに生成されたクライアント側アセットと発行された ASP.NET Core の成果物を組み合わせることは面倒なことができます。</span><span class="sxs-lookup"><span data-stu-id="2d339-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="2d339-267">さいわいにも、SpaServices がという名前のカスタム MSBuild ターゲットとそのパブリケーション全体のプロセスを調整`RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="2d339-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="2d339-268">MSBuild ターゲットでは、次の責任があります。</span><span class="sxs-lookup"><span data-stu-id="2d339-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="2d339-269">Npm パッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="2d339-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="2d339-270">サードパーティのクライアント側アセットの実稼働レベルのビルドを作成します。</span><span class="sxs-lookup"><span data-stu-id="2d339-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="2d339-271">カスタムクライアント側アセットの実稼働レベルのビルドを作成します。</span><span class="sxs-lookup"><span data-stu-id="2d339-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="2d339-272">Webpack によって生成されたアセットを publish フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="2d339-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="2d339-273">MSBuild ターゲットが実行するときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2d339-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="2d339-274">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2d339-274">Additional resources</span></span>

* [<span data-ttu-id="2d339-275">Angular Docs</span><span class="sxs-lookup"><span data-stu-id="2d339-275">Angular Docs</span></span>](https://angular.io/docs)
