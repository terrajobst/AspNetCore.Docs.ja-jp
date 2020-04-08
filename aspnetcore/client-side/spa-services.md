---
title: ASP.NET Core で JavaScript サービスを使用してシングル ページ アプリケーションを作成する
author: scottaddie
description: ASP.NET Core と JavaScript サービスを使用してシングル ページ アプリケーション (SPA) を作成する利点について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649220"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="00f76-103">ASP.NET Core で JavaScript サービスを使用してシングル ページ アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="00f76-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="00f76-104">作成者: [Scott Addie](https://github.com/scottaddie)、[Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="00f76-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="00f76-105">シングル ページ アプリケーション (SPA) は、本質的に高度なユーザー エクスペリエンスを提供できることから、広く使われているタイプの Web アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="00f76-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="00f76-106">クライアント側の SPA フレームワークまたはライブラリ ([Angular](https://angular.io/) や [React](https://facebook.github.io/react/) など) と、ASP.NET Core などのサーバー側のフレームワークの統合は、困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="00f76-107">JavaScript サービスは、統合プロセスの手間を減らすために開発されました。</span><span class="sxs-lookup"><span data-stu-id="00f76-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="00f76-108">これにより、さまざまなクライアントやサーバーのテクノロジ スタック間でシームレスな操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="00f76-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="00f76-109">この記事で説明されている機能は、ASP.NET Core 3.0 時点で互換性のために残されてます。</span><span class="sxs-lookup"><span data-stu-id="00f76-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="00f76-110">より単純な SPA フレームワーク統合メカニズムが [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet パッケージで利用できます。</span><span class="sxs-lookup"><span data-stu-id="00f76-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="00f76-111">詳細については、[Microsoft.AspNetCore.SpaServices と Microsoft.AspNetCore.NodeServices の廃止に関するお知らせのページ](https://github.com/dotnet/AspNetCore/issues/12890)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00f76-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="00f76-112">JavaScript サービスとは</span><span class="sxs-lookup"><span data-stu-id="00f76-112">What is JavaScript Services</span></span>

<span data-ttu-id="00f76-113">JavaScript サービスは、ASP.NET Core のクライアント側テクノロジのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="00f76-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="00f76-114">その目的は、ASP.NET Core を開発者の SPA 構築に有用なサーバー側プラットフォームとして位置付けることにあります。</span><span class="sxs-lookup"><span data-stu-id="00f76-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="00f76-115">JavaScript サービスは、次の 2 つの異なる NuGet パッケージで構成されています。</span><span class="sxs-lookup"><span data-stu-id="00f76-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="00f76-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="00f76-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="00f76-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="00f76-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="00f76-118">これらのパッケージは、次のシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="00f76-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="00f76-119">サーバーで JavaScript を実行する</span><span class="sxs-lookup"><span data-stu-id="00f76-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="00f76-120">SPA フレームワークまたは SPA ライブラリを使用する</span><span class="sxs-lookup"><span data-stu-id="00f76-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="00f76-121">Webpack を使用してクライアント側の資産を構築する</span><span class="sxs-lookup"><span data-stu-id="00f76-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="00f76-122">この記事では、SpaServices パッケージを使用することに重点を置いて説明します。</span><span class="sxs-lookup"><span data-stu-id="00f76-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="00f76-123">SpaServices とは</span><span class="sxs-lookup"><span data-stu-id="00f76-123">What is SpaServices</span></span>

<span data-ttu-id="00f76-124">SpaServices は、ASP.NET Core を開発者の SPA 構築に有用なサーバー側プラットフォームとして位置付けるために作成されました。</span><span class="sxs-lookup"><span data-stu-id="00f76-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="00f76-125">SpaServices は、ASP.NET Core で SPA を開発する際に必ず必要になるのものではありません。また、SpaServices を使っても、開発者が特定のクライアント フレームワークにロックされることはありません。</span><span class="sxs-lookup"><span data-stu-id="00f76-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="00f76-126">SpaServices は、次のような便利なインフラストラクチャを提供します。</span><span class="sxs-lookup"><span data-stu-id="00f76-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="00f76-127">サーバー側の事前レンダリング</span><span class="sxs-lookup"><span data-stu-id="00f76-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="00f76-128">Webpack Dev ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="00f76-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="00f76-129">ホット モジュール置換</span><span class="sxs-lookup"><span data-stu-id="00f76-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="00f76-130">ルーティング ヘルパー</span><span class="sxs-lookup"><span data-stu-id="00f76-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="00f76-131">これらのインフラストラクチャ コンポーネントを集めると、開発ワークフローとランタイム エクスペリエンスの両方を強化することができます。</span><span class="sxs-lookup"><span data-stu-id="00f76-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="00f76-132">このコンポーネントは、個別に採用できます。</span><span class="sxs-lookup"><span data-stu-id="00f76-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="00f76-133">SpaServices を使用するための前提条件</span><span class="sxs-lookup"><span data-stu-id="00f76-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="00f76-134">SpaServices を使用するには、以下をインストールします。</span><span class="sxs-lookup"><span data-stu-id="00f76-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="00f76-135">[Node.js](https://nodejs.org/) (バージョン 6 以降) と npm</span><span class="sxs-lookup"><span data-stu-id="00f76-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="00f76-136">これらのコンポーネントがインストールされていて検出可能であることを確認するには、コマンド ラインから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="00f76-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="00f76-137">Azure の Web サイトにデプロイする場合、操作は必要ありません &mdash; サーバー環境に Node.js がインストールされて使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="00f76-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="00f76-138">Visual Studio 2017 を使用している Windows では、 **.NET Core クロスプラットフォーム開発**ワークロードを選択すると SDK がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="00f76-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="00f76-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="00f76-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="00f76-140">サーバー側の事前レンダリング</span><span class="sxs-lookup"><span data-stu-id="00f76-140">Server-side prerendering</span></span>

<span data-ttu-id="00f76-141">ユニバーサル (アイソモーフィックとも呼ばれます) アプリケーションは、サーバーとクライアントの両方で実行できる JavaScript アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="00f76-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="00f76-142">Angular、React などの一般的なフレームワークは、このアプリケーション開発スタイル用のユニバーサル プラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="00f76-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="00f76-143">まず、サーバーで Node.js を使用してフレームワーク コンポーネントをレンダリングし、続く処理の実行をクライアントに委任します。</span><span class="sxs-lookup"><span data-stu-id="00f76-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="00f76-144">SpaServices が提供する ASP.NET Core [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は、サーバーで JavaScript 関数を呼び出すことで、サーバー側の事前レンダリングの実装が簡単にします。</span><span class="sxs-lookup"><span data-stu-id="00f76-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="00f76-145">サーバー側の事前レンダリングの前提条件</span><span class="sxs-lookup"><span data-stu-id="00f76-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="00f76-146">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="00f76-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="00f76-147">サーバー側の事前レンダリングの構成</span><span class="sxs-lookup"><span data-stu-id="00f76-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="00f76-148">プロジェクトの *_ViewImports.cshtml* ファイルに名前空間を登録すると、タグ ヘルパーを検出できるようになります。</span><span class="sxs-lookup"><span data-stu-id="00f76-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="00f76-149">タグヘルパーは、Razor ビューで HTML に似た構文を利用することで、低レベルの API と直接通信する複雑な部分を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="00f76-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="00f76-150">asp-prerender-module タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="00f76-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="00f76-151">前のコード例で使用されている `asp-prerender-module` タグ ヘルパーは、サーバーの Node.js で *ClientApp/dist/main-server.js* を実行します。</span><span class="sxs-lookup"><span data-stu-id="00f76-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="00f76-152">わかりやすくするために、*main-server.js* ファイルは、[Webpack](https://webpack.github.io/) ビルド プロセスで TypeScript から JavaScript へのトランスパイル タスクの成果物とします。</span><span class="sxs-lookup"><span data-stu-id="00f76-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="00f76-153">Webpack では `main-server` のエントリ ポイントの別名が定義されています。また、この別名の依存関係グラフの走査は、*ClientApp/boot-server.ts* ファイルから開始します。</span><span class="sxs-lookup"><span data-stu-id="00f76-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="00f76-154">次の Angular の例では、*ClientApp/boot-server.ts* ファイルが `createServerRenderer` npm パッケージの `RenderResult` 関数と `aspnet-prerendering` 型を使用して Node.js によるサーバー レンダリングを構成しています。</span><span class="sxs-lookup"><span data-stu-id="00f76-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="00f76-155">サーバー側のレンダリング用の HTML マークアップは、resolve 関数呼び出しに渡されます。これは、厳密に型指定された JavaScript の `Promise` オブジェクトでラップされています。</span><span class="sxs-lookup"><span data-stu-id="00f76-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="00f76-156">`Promise` オブジェクトは、DOM のプレースホルダー要素に注入する HTML マークアップを非同期にページに渡すという重要な役割を果たしています。</span><span class="sxs-lookup"><span data-stu-id="00f76-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="00f76-157">asp-prerender-data タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="00f76-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="00f76-158">`asp-prerender-module` タグ ヘルパーと `asp-prerender-data` タグ ヘルパーと組み合わせると、Razor ビューからサーバー側 JavaScript にコンテキスト情報を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="00f76-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="00f76-159">たとえば、次のマークアップは、ユーザー データを `main-server` モジュールに渡します。</span><span class="sxs-lookup"><span data-stu-id="00f76-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="00f76-160">渡された `UserName` 引数は、組み込みの JSON シリアライザーによってシリアル化され、`params.data` オブジェクトに格納されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="00f76-161">次の Angular の例では、このデータを使用して、`h1` の要素内に独自の挨拶文を作成します。</span><span class="sxs-lookup"><span data-stu-id="00f76-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="00f76-162">タグ ヘルパーで渡されるプロパティ名は、**パスカルケース**表記で表されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="00f76-163">同じプロパティ名が**キャメルケース**で表される JavaScript とは対照的です。</span><span class="sxs-lookup"><span data-stu-id="00f76-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="00f76-164">既定の JSON シリアル化構成が、この違いに対応します。</span><span class="sxs-lookup"><span data-stu-id="00f76-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="00f76-165">上のコード例を発展させると、サーバーからビューにデータを渡すことができます。そのためには、`globals` 関数に渡す `resolve` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="00f76-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="00f76-166">`postList` オブジェクト内で定義されている `globals` 配列は、ブラウザーのグローバル `window` オブジェクトにアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="00f76-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="00f76-167">この変数はグローバル スコープに含められ、それによって作業の重複がなくなります。具体的に言えば、同じデータをサーバーで一度、クライアントで再度読み込むことがなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="00f76-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![window オブジェクトにアタッチされたグローバル postList 変数](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="00f76-169">Webpack Dev ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="00f76-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="00f76-170">[Webpack Dev ミドルウェア](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) は、Webpack が必要に応じてリソースを構築することで、合理化された開発ワークフローを実現します。</span><span class="sxs-lookup"><span data-stu-id="00f76-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="00f76-171">ページがブラウザーに再読み込みされると、ミドルウェアはクライアント側のリソースを自動的にコンパイルして提供します。</span><span class="sxs-lookup"><span data-stu-id="00f76-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="00f76-172">別の方法として、サードパーティの依存関係またはカスタム コードが変更されたときに、プロジェクトの npm ビルド スクリプトを介して Webpack を手動で呼び出す方法もあります。</span><span class="sxs-lookup"><span data-stu-id="00f76-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="00f76-173">次の例では、*package.json* ファイル内の npm ビルド スクリプトを示しています。</span><span class="sxs-lookup"><span data-stu-id="00f76-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="00f76-174">Webpack Dev ミドルウェアの前提条件</span><span class="sxs-lookup"><span data-stu-id="00f76-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="00f76-175">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="00f76-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="00f76-176">Webpack Dev ミドルウェアの構成</span><span class="sxs-lookup"><span data-stu-id="00f76-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="00f76-177">Webpack Dev ミドルウェアは、*Startup.cs* ファイルの `Configure` メソッドにある次のコードによって、HTTP 要求パイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="00f76-178">`UseWebpackDevMiddleware` 拡張メソッドを使用して[静的ファイル ホスティングを登録](xref:fundamentals/static-files)する前に、`UseStaticFiles` 拡張メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="00f76-179">セキュリティ上の理由から、アプリが開発モードで実行されている場合にのみ、ミドルウェアを登録してください。</span><span class="sxs-lookup"><span data-stu-id="00f76-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="00f76-180">*webpack.config.js* ファイルの `output.publicPath` プロパティは、`dist` フォルダーの変更を監視するようにミドルウェアに指示します。</span><span class="sxs-lookup"><span data-stu-id="00f76-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="00f76-181">ホット モジュール置換</span><span class="sxs-lookup"><span data-stu-id="00f76-181">Hot Module Replacement</span></span>

<span data-ttu-id="00f76-182">Webpack の[ホット モジュール置換](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) 機能は、[Webpack Dev ミドルウェア](#webpack-dev-middleware)が進化したものと考えることができます。</span><span class="sxs-lookup"><span data-stu-id="00f76-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="00f76-183">HMR は同じ利点をすべて実現しますが、変更をコンパイルした後にページ コンテンツを自動的に更新するので、開発ワークフローがさらに効率化されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="00f76-184">これをブラウザーの更新と混同しないでください。ブラウザーの更新は、現在のメモリ内の状態と SPA のデバッグ セッションに影響します。</span><span class="sxs-lookup"><span data-stu-id="00f76-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="00f76-185">Webpack Dev ミドルウェア サービスとブラウザーの間には、ライブ リンクがあります。これは、変更がブラウザーにプッシュされることを意味します。</span><span class="sxs-lookup"><span data-stu-id="00f76-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="00f76-186">ホット モジュール置換の前提条件</span><span class="sxs-lookup"><span data-stu-id="00f76-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="00f76-187">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="00f76-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="00f76-188">ホット モジュール置換の構成</span><span class="sxs-lookup"><span data-stu-id="00f76-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="00f76-189">HMR コンポーネントは、`Configure` メソッドで MVC の HTTP 要求パイプラインに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="00f76-190">[Webpack Dev ミドルウェア](#webpack-dev-middleware)の場合と同様に、`UseWebpackDevMiddleware` 拡張メソッドを `UseStaticFiles` 拡張メソッドの前に呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="00f76-191">セキュリティ上の理由から、アプリが開発モードで実行されている場合にのみ、ミドルウェアを登録してください。</span><span class="sxs-lookup"><span data-stu-id="00f76-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="00f76-192">*webpack.config.js* ファイルでは、`plugins` 配列を定義する必要があります。これは空のままでもかまいません。</span><span class="sxs-lookup"><span data-stu-id="00f76-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="00f76-193">ブラウザーでアプリを読み込むと、開発者ツールのコンソールのタブに HMR がアクティブ化されたことの確認が表示されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![ホット モジュール置換の接続メッセージ](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="00f76-195">ルーティング ヘルパー</span><span class="sxs-lookup"><span data-stu-id="00f76-195">Routing helpers</span></span>

<span data-ttu-id="00f76-196">ほとんどの ASP.NET Core ベースの SPA では、サーバー側のルーティングに加えて、クライアント側のルーティングが必要になります。</span><span class="sxs-lookup"><span data-stu-id="00f76-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="00f76-197">SPA および MVC のルーティング システムは、干渉せずに個別に動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="00f76-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="00f76-198">しかし、1 つのエッジケースでは、HTTP 応答 404 を認識するという課題があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="00f76-199">拡張子のない `/some/page` というルートを使用するシナリオを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="00f76-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="00f76-200">要求がサーバー側ルートとはパターン一致せず、クライアント側のルートと一致すると仮定します。</span><span class="sxs-lookup"><span data-stu-id="00f76-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="00f76-201">ここで、`/images/user-512.png` の受信要求について考えてみます。通常は、サーバー上のイメージ ファイルを検索する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="00f76-202">要求されたリソース パスがどのサーバー側ルートまたは静的ファイルとも一致しない場合、クライアント側アプリケーションがこれを処理することはほとんどありません &mdash; 通常は、HTTP 状態コード 404 が返されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="00f76-203">ルーティング ヘルパーの前提条件</span><span class="sxs-lookup"><span data-stu-id="00f76-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="00f76-204">クライアント側ルーティング npm パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="00f76-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="00f76-205">Angular を使用する場合の例を示します。</span><span class="sxs-lookup"><span data-stu-id="00f76-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="00f76-206">ルーティング ヘルパーの構成</span><span class="sxs-lookup"><span data-stu-id="00f76-206">Routing helpers configuration</span></span>

<span data-ttu-id="00f76-207">`MapSpaFallbackRoute` メソッドで `Configure` という名前の拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="00f76-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="00f76-208">ルートは、構成された順序で評価されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="00f76-209">そのため、上のコード例の `default` ルートは、最初にパターン マッチングに使用されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="00f76-210">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="00f76-210">Create a new project</span></span>

<span data-ttu-id="00f76-211">JavaScript サービスには、事前構成済みのアプリケーション テンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="00f76-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="00f76-212">これらのテンプレートでは、Angular、React、Redux などのさまざまなフレームワークやライブラリと共に SpaServices を使用します。</span><span class="sxs-lookup"><span data-stu-id="00f76-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="00f76-213">これらのテンプレートは、次のコマンドを実行して .NET Core CLI 経由でインストールできます。</span><span class="sxs-lookup"><span data-stu-id="00f76-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="00f76-214">使用可能な SPA テンプレートの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="00f76-215">テンプレート</span><span class="sxs-lookup"><span data-stu-id="00f76-215">Templates</span></span>                                 | <span data-ttu-id="00f76-216">短い形式の名前</span><span class="sxs-lookup"><span data-stu-id="00f76-216">Short Name</span></span> | <span data-ttu-id="00f76-217">言語</span><span class="sxs-lookup"><span data-stu-id="00f76-217">Language</span></span> | <span data-ttu-id="00f76-218">Tags</span><span class="sxs-lookup"><span data-stu-id="00f76-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="00f76-219">MVC ASP.NET Core with Angular</span><span class="sxs-lookup"><span data-stu-id="00f76-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="00f76-220">angular</span><span class="sxs-lookup"><span data-stu-id="00f76-220">angular</span></span>    | <span data-ttu-id="00f76-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="00f76-221">[C#]</span></span>     | <span data-ttu-id="00f76-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="00f76-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="00f76-223">MVC ASP.NET Core with React.js</span><span class="sxs-lookup"><span data-stu-id="00f76-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="00f76-224">react</span><span class="sxs-lookup"><span data-stu-id="00f76-224">react</span></span>      | <span data-ttu-id="00f76-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="00f76-225">[C#]</span></span>     | <span data-ttu-id="00f76-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="00f76-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="00f76-227">MVC ASP.NET Core with React.js and Redux</span><span class="sxs-lookup"><span data-stu-id="00f76-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="00f76-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="00f76-228">reactredux</span></span> | <span data-ttu-id="00f76-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="00f76-229">[C#]</span></span>     | <span data-ttu-id="00f76-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="00f76-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="00f76-231">SPA テンプレートの 1 つを使用して新しいプロジェクトを作成するには、**dotnet new** コマンドでテンプレートの[短い形式の名前](/dotnet/core/tools/dotnet-new)を指定します。</span><span class="sxs-lookup"><span data-stu-id="00f76-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="00f76-232">次のコマンドは、サーバー側用に構成された ASP.NET Core MVC を使用して、Angular アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="00f76-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="00f76-233">ランタイム構成モードの設定</span><span class="sxs-lookup"><span data-stu-id="00f76-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="00f76-234">2 つのプライマリ ランタイム構成モードがあります。</span><span class="sxs-lookup"><span data-stu-id="00f76-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="00f76-235">**開発**:</span><span class="sxs-lookup"><span data-stu-id="00f76-235">**Development**:</span></span>
  * <span data-ttu-id="00f76-236">デバッグを容易にするソース マップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="00f76-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="00f76-237">パフォーマンス向上のためにクライアント側のコードを最適化することはしません。</span><span class="sxs-lookup"><span data-stu-id="00f76-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="00f76-238">**運用**:</span><span class="sxs-lookup"><span data-stu-id="00f76-238">**Production**:</span></span>
  * <span data-ttu-id="00f76-239">ソース マップを除外します。</span><span class="sxs-lookup"><span data-stu-id="00f76-239">Excludes source maps.</span></span>
  * <span data-ttu-id="00f76-240">バンドルと縮小を使用して、クライアント側のコードを最適化します。</span><span class="sxs-lookup"><span data-stu-id="00f76-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="00f76-241">ASP.NET Core では、`ASPNETCORE_ENVIRONMENT` という名前の環境変数を使用して構成モードを格納します。</span><span class="sxs-lookup"><span data-stu-id="00f76-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="00f76-242">詳細については、「[環境を設定する](xref:fundamentals/environments#set-the-environment)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="00f76-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="00f76-243">.NET Core CLI で実行する</span><span class="sxs-lookup"><span data-stu-id="00f76-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="00f76-244">プロジェクト ルートで次のコマンドを実行して、必要な NuGet パッケージと npm パッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="00f76-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="00f76-245">アプリケーションをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="00f76-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="00f76-246">アプリケーションは、[ランタイム構成モード](#set-the-runtime-configuration-mode)に従って、localhost で開始されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="00f76-247">ブラウザーで `http://localhost:5000` に移動すると、ランディング ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="00f76-248">Visual Studio 2017 で実行する</span><span class="sxs-lookup"><span data-stu-id="00f76-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="00f76-249">*dotnet new* コマンドで生成された [.csproj](/dotnet/core/tools/dotnet-new) ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="00f76-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="00f76-250">必要な NuGet および npm パッケージは、プロジェクトを開いたときに自動的に復元されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="00f76-251">この復元プロセスには数分かかる場合があり、これが完了するとアプリケーションを実行する準備ができています。</span><span class="sxs-lookup"><span data-stu-id="00f76-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="00f76-252">緑色の実行ボタンをクリックするか `Ctrl + F5` キーを押すと、ブラウザーが開いてアプリケーションのランディング ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="00f76-253">アプリケーションは、[ランタイム構成モード](#set-the-runtime-configuration-mode)に従って、localhost で実行されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="00f76-254">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="00f76-254">Test the app</span></span>

<span data-ttu-id="00f76-255">SpaServices テンプレートは、[Karma](https://karma-runner.github.io/1.0/index.html) と [Jasmine](https://jasmine.github.io/) を使用してクライアント側のテストを実行するように事前構成されています。</span><span class="sxs-lookup"><span data-stu-id="00f76-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="00f76-256">Jasmine は JavaScript 用の一般的な単体テスト フレームワークであるのに対し、Karma はそれらのテストのテスト ランナーです。</span><span class="sxs-lookup"><span data-stu-id="00f76-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="00f76-257">Karma は [Webpack Dev ミドルウェア](#webpack-dev-middleware)と連携するように構成されています。これにより、開発者は、変更が行われるたびにテストを停止して実行する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="00f76-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="00f76-258">テスト ケースまたはテスト ケース自体に対してコードが実行されているかどうかにかかわらず、テストは自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="00f76-259">例として、Angular アプリケーションを使用します。`CounterComponent`counter.component.spec.ts*ファイルの* に対し、2 つの Jasmine テスト ケースが既に提供されています。</span><span class="sxs-lookup"><span data-stu-id="00f76-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="00f76-260">*ClientApp* ディレクトリで、コマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="00f76-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="00f76-261">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="00f76-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="00f76-262">このスクリプトは、Karma テスト ランナーを起動します。これにより、*karma.conf.js* ファイルで定義されている設定が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="00f76-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="00f76-263">その他の設定として、*karma.conf.js* の `files` 配列で実行するテスト ファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="00f76-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="00f76-264">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="00f76-264">Publish the app</span></span>

<span data-ttu-id="00f76-265">Azure への発行の詳細については、[こちらの GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/12474)のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="00f76-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="00f76-266">生成されたクライアント側アセットと発行された ASP.NET Core 成果物をすぐにデプロイ可能なパッケージにまとめると、煩雑になることがあります。</span><span class="sxs-lookup"><span data-stu-id="00f76-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="00f76-267">ありがたいことに、SpaServices は、`RunWebpack` という名前のカスタム MSBuild ターゲットを使用して、発行プロセス全体を調整します。</span><span class="sxs-lookup"><span data-stu-id="00f76-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="00f76-268">この MSBuild ターゲットには、次の役割があります。</span><span class="sxs-lookup"><span data-stu-id="00f76-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="00f76-269">npm パッケージを復元する</span><span class="sxs-lookup"><span data-stu-id="00f76-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="00f76-270">サードパーティ製クライアント側アセットの実稼働レベルのビルドを作成する</span><span class="sxs-lookup"><span data-stu-id="00f76-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="00f76-271">カスタムのクライアント側アセットの実稼働レベルのビルドを作成する</span><span class="sxs-lookup"><span data-stu-id="00f76-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="00f76-272">Webpack が生成したアセットを発行フォルダーにコピーする</span><span class="sxs-lookup"><span data-stu-id="00f76-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="00f76-273">次を実行すると、MSBuild ターゲットが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="00f76-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="00f76-274">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="00f76-274">Additional resources</span></span>

* [<span data-ttu-id="00f76-275">Angular ドキュメント</span><span class="sxs-lookup"><span data-stu-id="00f76-275">Angular Docs</span></span>](https://angular.io/docs)
