---
title: ASP.NET Core のルーティング
author: rick-anderson
description: ASP.NET Core のルーティングでどのように要求 URI をエンドポイント セレクターにマッピングし、受信要求をエンドポイントに配布するかについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: fundamentals/routing
ms.openlocfilehash: 71cb7215651a263e588531c9be644326c0b6eda6
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167089"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="01b9a-103">ASP.NET Core のルーティング</span><span class="sxs-lookup"><span data-stu-id="01b9a-103">Routing in ASP.NET Core</span></span>

<span data-ttu-id="01b9a-104">[Ryan Nowak](https://github.com/rynowak)、[Steve Smith](https://ardalis.com/)、[Rick Anderson](https://twitter.com/RickAndMSFT) 作</span><span class="sxs-lookup"><span data-stu-id="01b9a-104">By [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="01b9a-105">このトピックのバージョン 1.1 については、「[Routing in ASP.NET Core](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf)」 (ASP.NET Core のルーティング) (バージョン 1.1、PDF) をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-105">For the 1.1 version of this topic, download [Routing in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-106">ルーティングでは、要求 URI をエンドポイント セレクターにマッピングし、受信要求をエンドポイントに配布します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-106">Routing is responsible for mapping request URIs to endpoint selectors and dispatching incoming requests to endpoints.</span></span> <span data-ttu-id="01b9a-107">ルートはアプリに定義され、アプリの起動時に構成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-107">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="01b9a-108">ルートは、要求に含まれている URL から値を任意で抽出できます。その値を要求処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-108">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="01b9a-109">ルーティングでは、アプリからのルート情報を利用し、エンドポイント セレクターにマッピングする URL を生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-109">Using route information from the app, routing is also able to generate URLs that map to endpoint selectors.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="01b9a-110">ASP.NET Core 2.2 で最新のルーティング シナリオを使用するには、`Startup.ConfigureServices` で[互換性バージョン](xref:mvc/compatibility-version)を MVC サービス登録に指定します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-110">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="01b9a-111"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> オプションでは、ルーティングで内部的にエンドポイント ベースのロジックを使用するか、ASP.NET Core 2.1 以前の <xref:Microsoft.AspNetCore.Routing.IRouter> ベースのロジックを使用する必要があるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-111">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="01b9a-112">互換性バージョンが 2.2 以降に設定されている場合、既定値は `true` となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-112">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="01b9a-113">以前のルーティング ロジックを使用する場合は、値を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-113">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="01b9a-114"><xref:Microsoft.AspNetCore.Routing.IRouter> ベースのルーティングの詳細については、[このトピックの ASP.NET Core 2.1 バージョン](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-114">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-115">ルーティングでは要求 URI をルート ハンドラーにマッピングし、受信要求を配布します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-115">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="01b9a-116">ルートはアプリに定義され、アプリの起動時に構成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-116">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="01b9a-117">ルートは、要求に含まれている URL から値を任意で抽出できます。その値を要求処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-117">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="01b9a-118">ルーティングでは、アプリからの構成済みルートを使用して、ルート ハンドラーにマッピングする URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-118">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="01b9a-119">ASP.NET Core 2.1 で最新のルーティング シナリオを使用するには、`Startup.ConfigureServices` で[互換性バージョン](xref:mvc/compatibility-version)を MVC サービス登録に指定します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-119">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="01b9a-120">本文では、ASP.NET Core ルーティングについて詳しく取り上げます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-120">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="01b9a-121">ASP.NET Core MVC ルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-121">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="01b9a-122">Razor Pages のルーティング規則については、「<xref:razor-pages/razor-pages-conventions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-122">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="01b9a-123">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="01b9a-123">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="01b9a-124">ルーティングの基本</span><span class="sxs-lookup"><span data-stu-id="01b9a-124">Routing basics</span></span>

<span data-ttu-id="01b9a-125">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-125">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="01b9a-126">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="01b9a-126">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="01b9a-127">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-127">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="01b9a-128">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-128">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="01b9a-129">開発者は一般的に、特殊な状況でのアプリのトラフィックが多い領域 (ブログや E コマース エンドポイントなど) に対しては、[属性ルーティング](xref:mvc/controllers/routing#attribute-routing)または専用規則ルートを使用して簡易ルートをさらに追加します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-129">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="01b9a-130">Web API では、属性ルーティングを使用して、HTTP 動詞で操作を表現するリソースのセットとしてアプリの機能をモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-130">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="01b9a-131">つまり、同じ論理リソース上の多くの操作 (たとえば GET や POST) で、同じ URL を使用します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-131">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="01b9a-132">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-132">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="01b9a-133">Razor Pages アプリでは既定の規則ルーティングを使用して、アプリの *Pages* フォルダーの名前付きリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-133">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="01b9a-134">Razor Pages のルーティング動作をカスタマイズできる追加の規則を使用できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-134">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="01b9a-135">詳細については、次のトピックを参照してください。 <xref:razor-pages/index> および <xref:razor-pages/razor-pages-conventions></span><span class="sxs-lookup"><span data-stu-id="01b9a-135">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="01b9a-136">URL 生成サポートを使用すると、アプリを相互にリンクする URL をハード コーディングすることなくアプリを開発できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-136">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="01b9a-137">このサポートにより、基本的なルーティング構成を使用して作業を開始し、アプリのリソース レイアウトが決まった後でルートを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-137">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-138">ルーティングでは*エンドポイント* (`Endpoint`) を使用して、アプリの論理エンドポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-138">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="01b9a-139">エンドポイントでは、要求と、任意のメタデータのコレクションを処理するデリゲートが定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-139">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="01b9a-140">メタデータは、各エンドポイントにアタッチされている構成とポリシーに基づいて横断的な関心事を実装するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-140">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="01b9a-141">ルーティング システムには次の特性があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-141">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="01b9a-142">ルート テンプレート構文は、トークン化されたルート パラメーターでルートを定義するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-142">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="01b9a-143">規則スタイルおよび属性スタイルのエンドポイント構成が許可されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-143">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="01b9a-144"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> は、URL パラメーターに特定のエンドポイント制約で有効な値が含まれているかどうかを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-144"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="01b9a-145">MVC/Razor Pages などのアプリ モデルでは、ルーティング シナリオの予測可能な実装を持つ、すべてのエンドポイントを登録します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-145">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="01b9a-146">ルーティングの実装では、必要に応じて、ミドルウェア パイプラインでのルーティングに関する決定を行います。</span><span class="sxs-lookup"><span data-stu-id="01b9a-146">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="01b9a-147">ルーティング ミドルウェアの後に表示されるミドルウェアでは、特定の要求 URI に関するルーティング ミドルウェアのエンドポイント決定の結果を検査できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-147">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="01b9a-148">ミドルウェア パイプラインの任意の場所でアプリのすべてのエンドポイントを列挙することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-148">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="01b9a-149">アプリではルーティングを使用し、エンドポイント情報に基づいて URL (リダイレクトやリンクなど) を生成できます。そのため、URL をハードコーディングする必要がなく、保守管理が容易になります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-149">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="01b9a-150">URL の生成は、任意の拡張機能をサポートする、アドレスに基づきます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-150">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="01b9a-151">リンク ジェネレーター API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) は、URI を生成するために[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) を使用して任意の場所で解決できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-151">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="01b9a-152">リンク ジェネレーター API を DI で使用できない場合、<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> で URL を構築するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-152">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="01b9a-153">ASP.NET Core 2.2 のエンドポイント ルーティングのリリースでは、エンドポイント リンクは、MVC/Razor Pages アクションおよびページに制限されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-153">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="01b9a-154">エンドポイント リンク機能の拡張は、今後のリリースで予定されています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-154">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-155">ルーティングにおける*ルート* (<xref:Microsoft.AspNetCore.Routing.IRouter> の実装) の利用目的:</span><span class="sxs-lookup"><span data-stu-id="01b9a-155">Routing uses *routes* (implementations of <xref:Microsoft.AspNetCore.Routing.IRouter>) to:</span></span>

* <span data-ttu-id="01b9a-156">受信要求を*ルート ハンドラー*にマッピングする。</span><span class="sxs-lookup"><span data-stu-id="01b9a-156">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="01b9a-157">応答で使用される URL を生成する。</span><span class="sxs-lookup"><span data-stu-id="01b9a-157">Generate the URLs used in responses.</span></span>

<span data-ttu-id="01b9a-158">既定では、アプリにはルート コレクションが 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-158">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="01b9a-159">要求が到着すると、コレクション内のルートは、コレクションに存在する順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-159">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="01b9a-160">フレームワークでは、コレクション内の各ルートに対して <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> メソッドを呼び出し、受信要求 URL とコレクション内のルートとの照合を試みます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-160">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="01b9a-161">応答ではルーティングを使用し、ルート情報に基づいて URL (リダイレクトやリンクなど) を生成できます。そのため、URL をハードコーディングする必要がなく、保守管理が容易になります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-161">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="01b9a-162">ルーティング システムには次の特性があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-162">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="01b9a-163">ルート テンプレート構文は、トークン化されたルート パラメーターでルートを定義するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-163">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="01b9a-164">規則スタイルおよび属性スタイルのエンドポイント構成が許可されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-164">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="01b9a-165"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> は、URL パラメーターに特定のエンドポイント制約で有効な値が含まれているかどうかを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-165"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="01b9a-166">MVC/Razor Pages などのアプリ モデルでは、ルーティング シナリオの予測可能な実装を持つ、すべてのルートを登録します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-166">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="01b9a-167">応答ではルーティングを使用し、ルート情報に基づいて URL (リダイレクトやリンクなど) を生成できます。そのため、URL をハードコーディングする必要がなく、保守管理が容易になります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-167">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="01b9a-168">URL の生成は、任意の拡張機能をサポートする、ルートに基づきます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-168">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="01b9a-169"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> では、URL を構築するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-169"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-170">ルーティングは <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> クラスにより[ミドルウェア](xref:fundamentals/middleware/index) パイプラインに接続されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-170">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="01b9a-171">[ASP.NET Core MVC](xref:mvc/overview) では、ミドルウェア パイプラインにその構成の一部としてルーティングが追加され、MVC および Razor Pages アプリでのルーティングが処理されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-171">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="01b9a-172">スタンドアロン コンポーネントとしてルーティングを利用する方法については、「[ルーティング ミドルウェアの使用](#use-routing-middleware)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-172">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="01b9a-173">URL 一致</span><span class="sxs-lookup"><span data-stu-id="01b9a-173">URL matching</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-174">URL 一致というプロセスでは、ルーティングによって、受信要求が*エンドポイント*に配布されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-174">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="01b9a-175">このプロセスは URL パスのデータに基づきますが、要求内のあらゆるデータを考慮することもできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-175">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="01b9a-176">アプリの規模を大きくしたり、より複雑にしたりするとき、要求を個別のハンドラーに配布する機能が鍵となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-176">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01b9a-177">ルーティング ミドルウェアの実行時には、エンドポイント (`Endpoint`) が設定されて、<xref:Microsoft.AspNetCore.Http.HttpContext> 上の機能に値がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-177">When a Routing Middleware executes, it sets an endpoint (`Endpoint`) and route values to a feature on the <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="01b9a-178">現在の要求の場合:</span><span class="sxs-lookup"><span data-stu-id="01b9a-178">For the current request:</span></span>

* <span data-ttu-id="01b9a-179">`HttpContext.GetEndpoint` を呼び出すと、エンドポイントが取得されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-179">Calling `HttpContext.GetEndpoint` gets the endpoint.</span></span>
* <span data-ttu-id="01b9a-180">`HttpRequest.RouteValues` では、ルート値のコレクションが取得されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-180">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="01b9a-181">ルーティング ミドルウェアの後で実行されたミドルウェアでは、エンドポイントを参照し、アクションを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-181">Middleware running after the Routing Middleware can see the endpoint and take action.</span></span> <span data-ttu-id="01b9a-182">たとえば、認可ミドルウェアでは、エンドポイントのメタデータ コレクションに対し、認可ポリシーを問い合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-182">For example, an Authorization Middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="01b9a-183">要求処理パイプライン内のすべてのミドルウェアが実行された後、選択したエンドポイントのデリゲートが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-183">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-184">エンドポイント ルーティングのルーティング システムでは、配布に関するすべての決定が行われます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-184">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="01b9a-185">ミドルウェアでは選択されたエンドポイントに基づいてポリシーが適用されるため、セキュリティ ポリシーの適用や配布に影響する可能性のある決定は、ルーティング システム内で行うことが重要です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-185">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="01b9a-186">エンドポイントのデリゲートが実行されると、それまでに実行された要求処理に基づいて、[RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) のプロパティが適切な値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-186">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-187">URL 一致というプロセスでは、ルーティングによって、受信要求が*ハンドラー*に配布されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-187">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="01b9a-188">このプロセスは URL パスのデータに基づきますが、要求内のあらゆるデータを考慮することもできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-188">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="01b9a-189">アプリの規模を大きくしたり、より複雑にしたりするとき、要求を個別のハンドラーに配布する機能が鍵となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-189">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="01b9a-190">受信要求は <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> に入ります。これが各ルートで順に <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-190">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="01b9a-191"><xref:Microsoft.AspNetCore.Routing.IRouter> インスタンスでは、[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) を非 null の <xref:Microsoft.AspNetCore.Http.RequestDelegate> に設定することで、要求を*処理する*かどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-191">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="01b9a-192">要求に適したハンドラーがルートに見つかると、ルート処理が停止します。ハンドラーが呼び出され、要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-192">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="01b9a-193">要求を処理するためのルート ハンドラーが見つからない場合、ミドルウェアによって、要求が要求パイプラインの次のミドルウェアに渡されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-193">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="01b9a-194"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> に最初に入力されるのが、現在の要求に関連付けられている [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-194">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="01b9a-195">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) と [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) は、ルートが一致した後に設定される出力です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-195">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="01b9a-196">また、<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> を呼び出す一致が見つかると、それまでに実行された要求処理に基づいて、[RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) のプロパティが適切な値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-196">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-197">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) は、ルートから生成された*ルート値*のディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-197">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="01b9a-198">この値は通常、URL のトークン化で決定されます。この値を利用してユーザー入力を受け取ったり、アプリ内で決定をさらに配布したりできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-198">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="01b9a-199">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) は、一致したルートに関連する追加データのプロパティ バッグです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-199">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="01b9a-200">一致したルートに基づいてアプリで決定を行えるように、<xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> が提供されます。これは状態データと各ルートの関連付けをサポートするためのものです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-200"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="01b9a-201">この値は開発者が定義するものです。ルーティングの動作に影響を与えることは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="01b9a-201">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="01b9a-202">また、[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) に一時退避される値はどのような型でも構いません。一方、[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) の場合は、文字列間で変換できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-202">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="01b9a-203">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) は、過去に要求に一致したルートの一覧です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-203">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="01b9a-204">ルートはルートの中に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-204">Routes can be nested inside of one another.</span></span> <span data-ttu-id="01b9a-205"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> プロパティは、結果的に一致をもたらしたルートの論理ツリーを通るパスを表します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-205">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="01b9a-206">一般的に、<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> の最初の項目はルート コレクションであり、これは URL 生成に使用するものです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-206">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="01b9a-207"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> の最後の項目は、一致したルート ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-207">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="01b9a-208">LinkGenerator による URL の生成</span><span class="sxs-lookup"><span data-stu-id="01b9a-208">URL generation with LinkGenerator</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-209">URL 生成は、ルーティングにおいて、一連のルート値に基づいて URL パスを作成するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-209">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="01b9a-210">これにより、エンドポイントとそれにアクセスする URL を論理的に分離できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-210">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="01b9a-211">エンドポイント ルーティングには、リンク ジェネレーター API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-211">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="01b9a-212"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> は、DI から取得できるシングルトン サービスです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-212"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from DI.</span></span> <span data-ttu-id="01b9a-213">API は、実行中の要求のコンテキスト外で使用することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-213">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="01b9a-214">MVC の <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> と、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、HTML ヘルパー、[アクション結果](xref:mvc/controllers/actions)など、<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> に依存するシナリオではリンク ジェネレーターを使用して、リンク生成機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-214">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="01b9a-215">リンク ジェネレーターは、*アドレス* と*アドレス スキーム* の概念に基づいています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-215">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="01b9a-216">アドレス スキームは、リンク生成で考慮すべきエンドポイントを決定する方法です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-216">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="01b9a-217">たとえば、MVC/Razor Pages からの、多くのユーザーに馴染みのあるルート名やルート値シナリオは、アドレス スキームとして実装されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-217">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="01b9a-218">リンク ジェネレーターでは、次の拡張メソッドを使用して、MVC/Razor Pages アクションおよびページにリンクできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-218">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="01b9a-219">これらのメソッドのオーバーロードでは、`HttpContext` を含む引数が受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-219">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="01b9a-220">これらのメソッドは `Url.Action` および `Url.Page` と機能的には同等ですが、柔軟性とオプションがさらに提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-220">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="01b9a-221">`GetPath*` メソッドは、絶対パスを含む URI を生成するという点で `Url.Action` および `Url.Page` に最も似ています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-221">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="01b9a-222">`GetUri*` メソッドでは常に、スキームとホストを含む絶対 URI が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-222">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="01b9a-223">`HttpContext` を受け入れるメソッドでは、実行中の要求のコンテキストで URI が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-223">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="01b9a-224">実行中の要求からのアンビエント ルート値、URL ベース パス、スキーム、およびホストは、オーバーライドされない限り使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-224">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="01b9a-225"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> はアドレスと共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-225"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="01b9a-226">URI の生成は、次の 2 つの手順で行われます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-226">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="01b9a-227">アドレスは、そのアドレスと一致するエンドポイントのリストにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-227">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="01b9a-228">各エンドポイントの `RoutePattern` は、指定された値と一致するルート パターンが見つかるまで評価されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-228">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="01b9a-229">結果の出力は、リンク ジェネレーターに指定された他の URI 部分と結合され、返されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-229">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="01b9a-230"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> によって提供されるメソッドでは、すべての種類のアドレスの標準的なリンク生成機能がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-230">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="01b9a-231">リンク ジェネレーターを使用する最も便利な方法は、特定のアドレスの種類の操作を実行する拡張メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-231">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="01b9a-232">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="01b9a-232">Extension Method</span></span>   | <span data-ttu-id="01b9a-233">説明</span><span class="sxs-lookup"><span data-stu-id="01b9a-233">Description</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="01b9a-234">指定された値に基づき、絶対パスを含む URI を生成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-234">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="01b9a-235">指定された値に基づき、絶対 URI を生成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-235">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="01b9a-236"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> メソッド呼び出しによる次の影響に注意してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-236">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="01b9a-237">受信要求の `Host` ヘッダーが確認されないアプリ構成では、`GetUri*` 拡張メソッドは注意して使用してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-237">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="01b9a-238">受信要求の `Host` ヘッダーが確認されていない場合、信頼されていない要求入力を、ビュー/ページの URI でクライアントに送り返すことができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-238">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="01b9a-239">すべての運用アプリで、`Host` ヘッダーを既知の有効な値と照らし合わせて確認するようにサーバーを構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="01b9a-239">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="01b9a-240">ミドルウェアで `Map` または `MapWhen` と組み合わせて、<xref:Microsoft.AspNetCore.Routing.LinkGenerator> を使用する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-240">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="01b9a-241">`Map*` では、実行中の要求の基本パスが変更され、リンク生成の出力に影響します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-241">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="01b9a-242">すべての <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API で基本パスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-242">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="01b9a-243">リンク生成への `Map*` の影響を元に戻すための空の基本パスを必ず指定してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-243">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="01b9a-244">以前のバージョンのルーティングとの相違点</span><span class="sxs-lookup"><span data-stu-id="01b9a-244">Differences from earlier versions of routing</span></span>

<span data-ttu-id="01b9a-245">ASP.NET Core 2.2 以降でのエンドポイント ルーティングと、ASP.NET Core での以前のバージョンのルーティングには、次のようないくつかの違いがあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-245">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="01b9a-246">エンドポイント ルーティング システムでは、<xref:Microsoft.AspNetCore.Routing.Route> からの継承を含む、<xref:Microsoft.AspNetCore.Routing.IRouter> ベースの拡張機能がサポートされません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-246">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="01b9a-247">エンドポイント ルーティングでは [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) がサポートされません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-247">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="01b9a-248">互換性 shim を引き続き使用するには、2.1 の[互換性バージョン](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-248">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="01b9a-249">エンドポイント ルーティングでは、規則ルートを使用する場合に生成される URI の大文字と小文字の指定の動作が異なります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-249">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="01b9a-250">次の既定のルート テンプレートについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-250">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="01b9a-251">次のルートを使用して、アクションへのリンクを生成するとします。</span><span class="sxs-lookup"><span data-stu-id="01b9a-251">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="01b9a-252"><xref:Microsoft.AspNetCore.Routing.IRouter> ベースのルーティングでは、このコードで `/blog/ReadPost/17` の URI が生成され、指定されたルート値の大文字と小文字の指定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-252">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="01b9a-253">ASP.NET Core 2.2 以降のエンドポイント ルーティングでは、`/Blog/ReadPost/17` ("Blog" の先頭は大文字) が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-253">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="01b9a-254">エンドポイント ルーティングでは `IOutboundParameterTransformer` インターフェイスが提供されます。それを使用して、この動作をグローバルにカスタマイズしたり、URL のマッピングで異なる規則を適用したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-254">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="01b9a-255">詳細については、「[パラメーター トランスフォーマー参照](#parameter-transformer-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-255">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="01b9a-256">存在しないコントローラー/アクションまたはページへのリンクを試みる場合、規則ルートがある MVC/Razor Pages によって使用されるリンク生成の動作は異なります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-256">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="01b9a-257">次の既定のルート テンプレートについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-257">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="01b9a-258">次のように既定のテンプレートを使用して、アクションへのリンクを生成するとします。</span><span class="sxs-lookup"><span data-stu-id="01b9a-258">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="01b9a-259">`IRouter` ベースのルーティングでは、`BlogController` が存在しない場合や `ReadPost` アクション メソッドがない場合でも、結果は常に `/Blog/ReadPost/17` となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-259">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="01b9a-260">予想どおり、アクション メソッドが存在する場合は、ASP.NET Core 2.2 以降のエンドポイント ルーティングで `/Blog/ReadPost/17` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-260">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="01b9a-261">*しかし、アクションが存在しない場合は、エンドポイント ルーティングで空の文字列が生成されます。*</span><span class="sxs-lookup"><span data-stu-id="01b9a-261">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="01b9a-262">概念的には、エンドポイント ルーティングでは、アクションが存在しない場合はエンドポイントが存在するとは見なされません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-262">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="01b9a-263">エンドポイント ルーティングで使用する場合は、リンク生成の*アンビエント値の無効化アルゴリズム* の動作が異なります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-263">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="01b9a-264">*アンビエント値の無効化* は、現在実行中の要求からのルート値 (アンビエント値) のうち、どちらがリンク生成操作で使用できのかを判断するアルゴリズムです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-264">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="01b9a-265">規則ルーティングでは、異なるアクションへのリンク時に余分なルート値が常に無効化されました。</span><span class="sxs-lookup"><span data-stu-id="01b9a-265">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="01b9a-266">属性ルーティングは、ASP.NET Core 2.2 のリリースより前ではこのように動作しませんでした。</span><span class="sxs-lookup"><span data-stu-id="01b9a-266">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="01b9a-267">以前のバージョンの ASP.NET Core では、同じルート パラメーター名を使用する別のアクションへのリンクでリンク生成エラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="01b9a-267">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="01b9a-268">ASP.NET Core 2.2 以降では、両方の形式のルーティングで、別のアクションへのリンク時に値が無効化されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-268">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="01b9a-269">ASP.NET Core 2.1 以前での次の例について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-269">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="01b9a-270">別のアクション (または別のページ) にリンクする場合、好ましくない方法でルート値が再利用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-270">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="01b9a-271">*/Pages/Store/Product.cshtml* の場合:</span><span class="sxs-lookup"><span data-stu-id="01b9a-271">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="01b9a-272">*/Pages/Login.cshtml* の場合:</span><span class="sxs-lookup"><span data-stu-id="01b9a-272">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="01b9a-273">ASP.NET Core 2.1 以前で URI が `/Store/Product/18` である場合、`@Url.Page("/Login")` によって Store/Info ページに生成されるリンクは `/Login/18` となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-273">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="01b9a-274">リンク先が完全にアプリの異なる部分であっても、18 という `id` の値が再利用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-274">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="01b9a-275">`/Login` ページのコンテキスト内の `id` ルート値は、商品 ID 値ではなく、ユーザー ID 値であると考えられます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-275">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="01b9a-276">ASP.NET Core 2.2 以降でのエンドポイント ルーティングでは、結果は `/Login` となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-276">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="01b9a-277">リンク先が異なるアクションやページである場合、アンビエント値は再利用されません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-277">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="01b9a-278">ラウンド トリップ ルート パラメーターの構文: 二重アスタリスク (`**`) キャッチオール パラメーター構文を使用する場合、スラッシュはエンコードされません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-278">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="01b9a-279">リンクの生成中に、ルーティング システムでは、スラッシュを除く二重アスタリスク (`**`) キャッチオール パラメーター (`{**myparametername}` など) でキャプチャされた値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="01b9a-279">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="01b9a-280">二重アスタリスク キャッチオールは、ASP.NET Core 2.2 以降の `IRouter` ベース ルーティングでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-280">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="01b9a-281">以前のバージョンの ASP.NET Core での単一のアスタリスク キャッチオール パラメーター構文 (`{*myparametername}`) は引き続きサポートされ、スラッシュはエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-281">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="01b9a-282">ルート</span><span class="sxs-lookup"><span data-stu-id="01b9a-282">Route</span></span>              | <span data-ttu-id="01b9a-283">以下で生成されるリンク</span><span class="sxs-lookup"><span data-stu-id="01b9a-283">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="01b9a-284">`/search/admin%2Fproducts` (スラッシュはエンコードされます)</span><span class="sxs-lookup"><span data-stu-id="01b9a-284">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="01b9a-285">ミドルウェアの例</span><span class="sxs-lookup"><span data-stu-id="01b9a-285">Middleware example</span></span>

<span data-ttu-id="01b9a-286">次の例では、ミドルウェアで <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API を使用して、商品をリストするアクション メソッドへのリンクを作成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-286">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="01b9a-287">リンク ジェネレーターは、クラスに挿入し、`GenerateLink` を呼び出すことで、アプリのどのクラスでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-287">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-288">URL 生成は、ルーティングにおいて、一連のルート値に基づいて URL パスを作成するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-288">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="01b9a-289">これにより、ルート ハンドラーとそれにアクセスする URL を論理的に分離できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-289">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="01b9a-290">URL 生成は同様の繰り返しプロセスに従いますが、最初にユーザーまたはフレームワーク コードでルート コレクションの <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-290">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="01b9a-291">非 null の <xref:Microsoft.AspNetCore.Routing.VirtualPathData> が返されるまで、各*ルート* で順番に <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-291">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="01b9a-292"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> の第一入力:</span><span class="sxs-lookup"><span data-stu-id="01b9a-292">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="01b9a-293">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="01b9a-293">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="01b9a-294">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="01b9a-294">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="01b9a-295">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="01b9a-295">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="01b9a-296">ルートでは <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> と <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> によって指定されるルート値を主に使用し、URL を生成できるかどうかと、どの値を含めるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-296">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="01b9a-297"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> は、現在の要求を照合することで生成された一連のルート値です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-297">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="01b9a-298">対照的に、<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> は、現在の操作に適した URL の生成方法を指定するルート値です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-298">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="01b9a-299">ルートで現在のコンテキストに関連付けられているサービスまたは追加データを取得する必要がある場合、<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-299">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="01b9a-300">[VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) は [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) のオーバーライド セットであると考えてください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-300">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="01b9a-301">URL 生成では、同じルートまたはルート値を利用することでリンクの URL 生成を生成するために、現在の要求からのルート値の再利用が試行されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-301">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="01b9a-302"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> の出力は <xref:Microsoft.AspNetCore.Routing.VirtualPathData> です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-302">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="01b9a-303"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> は <xref:Microsoft.AspNetCore.Routing.RouteData> の並列です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-303"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="01b9a-304"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> には出力 URL として <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> が含まれ、ルートで設定する追加プロパティがいくつか含まれます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-304"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="01b9a-305">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) プロパティには、ルートによって生成された*仮想パス*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-305">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="01b9a-306">必要に応じて、パスをさらに処理する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-306">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="01b9a-307">生成された URL を HTML で表示するには、アプリの基礎パスを先頭に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-307">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="01b9a-308">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) は URL を正常に生成したルートの参照です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-308">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="01b9a-309">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) プロパティは、URL を生成したルートに関連する追加データのディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-309">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="01b9a-310">これは [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) の並列です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-310">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

::: moniker-end

### <a name="create-routes"></a><span data-ttu-id="01b9a-311">ルートを作成する</span><span class="sxs-lookup"><span data-stu-id="01b9a-311">Create routes</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-312">ルーティングにより、<xref:Microsoft.AspNetCore.Routing.IRouter> の標準実装として <xref:Microsoft.AspNetCore.Routing.Route> クラスが与えられます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-312">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="01b9a-313"><xref:Microsoft.AspNetCore.Routing.Route> では*ルート テンプレート* 構文を利用して、<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> の呼び出し時に URL パスに対して照合するパターンを定義します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-313"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="01b9a-314"><xref:Microsoft.AspNetCore.Routing.Route> は同じルート テンプレートを利用し、<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> の呼び出し時に URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-314"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-315">ほとんどのアプリは、<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> を呼び出すか、<xref:Microsoft.AspNetCore.Routing.IRouteBuilder> で定義されている同様の拡張メソッドの 1 つを呼び出してルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-315">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="01b9a-316">いずれの <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 拡張メソッドでも、<xref:Microsoft.AspNetCore.Routing.Route> のインスタンスが作成され、それがルート コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-316">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-317"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> では、ルート ハンドラー パラメーターは受け入れられません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-317"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="01b9a-318"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> は、<xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> によって処理されるルートを追加するだけです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-318"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="01b9a-319">MVC でのルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-319">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-320"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> では、ルート ハンドラー パラメーターは受け入れられません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-320"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="01b9a-321"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> は、<xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> によって処理されるルートを追加するだけです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-321"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="01b9a-322">既定のハンドラーは `IRouter` であり、そのハンドラーで要求が処理されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-322">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="01b9a-323">たとえば、ASP.NET Core MVC は通常、利用できるコントローラーやアクションに一致する要求のみを処理する既定のハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-323">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="01b9a-324">MVC でのルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-324">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-325">次のコード例は、一般的な ASP.NET Core MVC ルート定義によって使用される <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼び出しの例です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-325">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="01b9a-326">このテンプレートでは URL パスが照合され、ルート値が抽出されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-326">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="01b9a-327">たとえば、`/Products/Details/17` というパスでは、`{ controller = Products, action = Details, id = 17 }` というルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-327">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="01b9a-328">ルート値は、URL パスをセグメントに分割し、各セグメントと、ルート テンプレートの*ルート パラメーター* 名を照合することで決定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-328">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="01b9a-329">ルート パラメーターが指定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-329">Route parameters are named.</span></span> <span data-ttu-id="01b9a-330">パラメーターは、中かっこ `{ ... }` でパラメーター名を囲むことで定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-330">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="01b9a-331">上のテンプレートでは URL パス `/` の照合も行い、`{ controller = Home, action = Index }` という値を生成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-331">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="01b9a-332">これは、ルート パラメーターの `{controller}` と `{action}` に既定値が与えられ、`id` ルート パラメーターが任意であるためです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-332">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="01b9a-333">ルート パラメーター名の後の等号 (`=`) とそれに続く値により、パラメーターの既定値が定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-333">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="01b9a-334">ルート パラメーター名の後の疑問符 (`?`) により、オプションのパラメーターが定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-334">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="01b9a-335">既定値のルート パラメーターはルートが一致するとルート値を*常に*生成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-335">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="01b9a-336">オプションのパラメーターは、対応する URL パス セグメントがなかった場合、ルート値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-336">Optional parameters don't produce a route value if there was no corresponding URL path segment.</span></span> <span data-ttu-id="01b9a-337">ルート テンプレートのシナリオと構文の詳しい説明については、「[ルート テンプレート参照](#route-template-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-337">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="01b9a-338">次の例では、ルート パラメーター定義 `{id:int}` により、`id` ルート パラメーターの[ルート制約](#route-constraint-reference)が定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-338">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="01b9a-339">このテンプレートは `/Products/Details/Apples` ではなく、`/Products/Details/17` のような URL パスを照合します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-339">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="01b9a-340">ルート制約は <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> を実装し、ルート値を調べて正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-340">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="01b9a-341">この例では、ルート値 `id` は整数に変換できなければなりません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-341">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="01b9a-342">フレームワークによって提供されるルート制約については、「[ルート制約参照](#route-constraint-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-342">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="01b9a-343"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> の追加オーバーロードは、`constraints`、`dataTokens`、`defaults` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-343">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="01b9a-344">このようなパラメーターは一般的に、匿名型のオブジェクトを渡し、匿名型のプロパティ名がルート パラメーター名と一致するというような使われ方をします。</span><span class="sxs-lookup"><span data-stu-id="01b9a-344">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="01b9a-345">次の <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 例では、同等のルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-345">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="01b9a-346">制約と既定値を定義するインライン構文は単純なルートの場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-346">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="01b9a-347">しかし、インライン構文でサポートされていない、データ トークンなどのシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-347">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="01b9a-348">次の例では、その他のいくつかのシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-348">The following example demonstrates a few additional scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="01b9a-349">上記のテンプレートでは `/Blog/All-About-Routing/Introduction` のような URL パスを照合し、値 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-349">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="01b9a-350">`controller` と `action` の既定のルート値は、テンプレートに対応するルート パラメーターがなくても、ルートにより生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-350">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="01b9a-351">既定値はルート テンプレートで指定できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-351">Default values can be specified in the route template.</span></span> <span data-ttu-id="01b9a-352">`article` ルート パラメーターは、ルート パラメーター名の前に二重アスタリスク (`**`) があるときに、*キャッチオール* として定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-352">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="01b9a-353">キャッチオール ルート パラメーターは、URL パスの残りの部分をキャプチャします。空の文字列も照合できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-353">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="01b9a-354">上記のテンプレートでは `/Blog/All-About-Routing/Introduction` のような URL パスを照合し、値 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-354">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="01b9a-355">`controller` と `action` の既定のルート値は、テンプレートに対応するルート パラメーターがなくても、ルートにより生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-355">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="01b9a-356">既定値はルート テンプレートで指定できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-356">Default values can be specified in the route template.</span></span> <span data-ttu-id="01b9a-357">`article` ルート パラメーターは、ルート パラメーター名の前にアスタリスク (`*`) があるときに、*キャッチオール* として定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-357">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="01b9a-358">キャッチオール ルート パラメーターは、URL パスの残りの部分をキャプチャします。空の文字列も照合できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-358">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-359">次の例では、ルート制約とデータ トークンを追加します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-359">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="01b9a-360">上記のテンプレートでは `/en-US/Products/5` のような URL パスを照合し、値 `{ controller = Products, action = Details, id = 5 }` とデータ トークン `{ locale = en-US }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-360">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![ローカル変数ウィンドウ トークン](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="01b9a-362">ルート クラス URL の生成</span><span class="sxs-lookup"><span data-stu-id="01b9a-362">Route class URL generation</span></span>

<span data-ttu-id="01b9a-363"><xref:Microsoft.AspNetCore.Routing.Route> クラスは、一連のルート値をそのルート テンプレートと組み合わせる方法でも URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-363">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="01b9a-364">論理的には、URL パスの照合とは逆のプロセスになります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-364">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="01b9a-365">URL 生成を理解する方法として、どのような URL を生成したいのかを考えてください。そして、ルート テンプレートがその URL にどのように照合されるのかを考えてください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-365">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="01b9a-366">どのような値が生成されるでしょうか。</span><span class="sxs-lookup"><span data-stu-id="01b9a-366">What values would be produced?</span></span> <span data-ttu-id="01b9a-367"><xref:Microsoft.AspNetCore.Routing.Route> クラスにおける URL 生成のしくみと大体同じになります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-367">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="01b9a-368">次の例では、一般的な ASP.NET Core MVC の既定のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-368">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="01b9a-369">ルート値が `{ controller = Products, action = List }` の場合、URL `/Products/List` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-369">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="01b9a-370">ルート値が対応ルート パラメーターの代替となり、URL パスが作られます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-370">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="01b9a-371">`id` は任意のルート パラメーターであるため、URL は `id` の値なしで正常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-371">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="01b9a-372">ルート値が `{ controller = Home, action = Index }` の場合、URL `/` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-372">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="01b9a-373">指定されたルート値は既定値と一致し、その既定値に対応するセグメントを省略しても問題は発生しません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-373">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="01b9a-374">生成された両方の URL では、次のルート定義 (`/Home/Index` と `/`) でラウンドトリップされ、URL の生成に使用された同じルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-374">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="01b9a-375">アプリで ASP.NET Core MVC が使用される場合、ルーティングを直接呼び出す代わりに、<xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> を使用して URL を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-375">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="01b9a-376">URL 生成の詳細については、「[URL 生成参照](#url-generation-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-376">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="01b9a-377">ルーティング ミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="01b9a-377">Use Routing Middleware</span></span>

<span data-ttu-id="01b9a-378">アプリのプロジェクト ファイルの [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-378">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="01b9a-379">`Startup.ConfigureServices` でサービス コンテナーにルーティングを追加した例:</span><span class="sxs-lookup"><span data-stu-id="01b9a-379">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="01b9a-380">ルートは `Startup.Configure` メソッドで設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-380">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="01b9a-381">サンプル アプリでは次の API を使用します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-381">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="01b9a-382"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; HTTP GET 要求のみを照合します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-382"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="01b9a-383">下の表は、応答と所与の URI をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-383">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="01b9a-384">URI</span><span class="sxs-lookup"><span data-stu-id="01b9a-384">URI</span></span>                    | <span data-ttu-id="01b9a-385">応答</span><span class="sxs-lookup"><span data-stu-id="01b9a-385">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="01b9a-386">Hello!</span><span class="sxs-lookup"><span data-stu-id="01b9a-386">Hello!</span></span> <span data-ttu-id="01b9a-387">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="01b9a-387">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="01b9a-388">Hello!</span><span class="sxs-lookup"><span data-stu-id="01b9a-388">Hello!</span></span> <span data-ttu-id="01b9a-389">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="01b9a-389">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="01b9a-390">Hello!</span><span class="sxs-lookup"><span data-stu-id="01b9a-390">Hello!</span></span> <span data-ttu-id="01b9a-391">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="01b9a-391">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="01b9a-392">要求はフォール スルーし、一致するものはありません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-392">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="01b9a-393">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="01b9a-393">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="01b9a-394">要求はフォール スルーし、HTTP GET のみが一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-394">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="01b9a-395">要求はフォール スルーし、一致するものはありません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-395">The request falls through, no match.</span></span>              |

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-396">ルートを 1 つ構成する場合、<xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> を呼び出し、`IRouter` インスタンスを渡します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-396">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="01b9a-397"><xref:Microsoft.AspNetCore.Routing.RouteBuilder> を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-397">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-398">このフレームワークでは、ルートを作成するために拡張メソッド セット (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-398">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-399"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> など、リストされているメソッドの一部では、<xref:Microsoft.AspNetCore.Http.RequestDelegate> が必要です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-399">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="01b9a-400"><xref:Microsoft.AspNetCore.Http.RequestDelegate> は、ルートが一致したとき、*ルート ハンドラー*として使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-400">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="01b9a-401">この仲間の他のメソッドでは、ルート ハンドラーとして使用されるミドルウェア パイプラインを構成できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-401">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="01b9a-402">`Map*` メソッドで、<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> などのハンドラーを受け入れない場合、<xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-402">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-403">`Map[Verb]` メソッドは制約を利用し、メソッド名の HTTP Verb にルートを制限します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-403">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="01b9a-404">たとえば、 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> や <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-404">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="01b9a-405">ルート テンプレート参照</span><span class="sxs-lookup"><span data-stu-id="01b9a-405">Route template reference</span></span>

<span data-ttu-id="01b9a-406">中括弧 (`{ ... }`) 内のトークンは*ルート パラメーター*を定義します。ルートが一致した場合、これがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-406">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="01b9a-407">1 つのルート セグメントで複数のルート パラメーターを定義できますが、リテラル値で区切る必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-407">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="01b9a-408">たとえば、`{controller=Home}{action=Index}` は有効なルートになりません。`{controller}` と `{action}` の間にリテラル値がないためです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-408">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="01b9a-409">このようなルート パラメーターには名前を付ける必要があります。付加的な属性を指定することもあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-409">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="01b9a-410">ルート パラメーター以外のリテラル テキスト (`{id}` など) とパス区切り `/` は URL のテキストに一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-410">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="01b9a-411">テキスト照合は復号された URL パスを基盤とし、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-411">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="01b9a-412">リテラル ルート パラメーターの区切り記号 (`{` または `}`) を照合するには、文字を繰り返して区切り記号をエスケープします (`{{` または `}}`)。</span><span class="sxs-lookup"><span data-stu-id="01b9a-412">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="01b9a-413">任意のファイル拡張子が付いたファイル名のキャプチャを試行する URL パターンには、追加の考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-413">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="01b9a-414">たとえば、テンプレート `files/{filename}.{ext?}` について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-414">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="01b9a-415">`filename` と `ext` の両方の値が存在するときに、両方の値が入力されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-415">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="01b9a-416">URL に `filename` の値だけが存在する場合、末尾のピリオド (`.`) は任意であるため、このルートは一致となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-416">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="01b9a-417">次の URL はこのルートに一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-417">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-418">ルート パラメーターのプレフィックスとしてアスタリスク (`*`) または二重アスタリスク (`**`) を使用し、URI の残りの部分にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-418">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="01b9a-419">これらは、*キャッチオール* パラメーターと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-419">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="01b9a-420">たとえば、`blog/{**slug}` は、`/blog` で始まり、その後に (`slug` ルート値に割り当てられる) 任意の値が続くあらゆる URI に一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-420">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="01b9a-421">キャッチオール パラメーターは空の文字列に一致することもあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-421">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="01b9a-422">キャッチオール パラメーターでは、パス区切り (`/`) 文字を含め、URL の生成にルートが使用されるときに適切な文字がエスケープされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-422">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="01b9a-423">たとえば、ルート値が `{ path = "my/path" }` のルート `foo/{*path}` では、`foo/my%2Fpath` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-423">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="01b9a-424">エスケープされたスラッシュに注意してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-424">Note the escaped forward slash.</span></span> <span data-ttu-id="01b9a-425">パス区切り文字をラウンドトリップさせるには、`**` ルート パラメーター プレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-425">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="01b9a-426">`{ path = "my/path" }` のルート `foo/{**path}` では、`foo/my/path` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-426">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01b9a-427">ルート パラメーターのプレフィックスとしてアスタリスク (`*`) を使用し、URI の残りの部分にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-427">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="01b9a-428">これは*キャッチオール* パラメーターと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-428">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="01b9a-429">たとえば、`blog/{*slug}` は、`/blog` で始まり、その後に (`slug` ルート値に割り当てられる) 任意の値が続くあらゆる URI に一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-429">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="01b9a-430">キャッチオール パラメーターは空の文字列に一致することもあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-430">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="01b9a-431">キャッチオール パラメーターでは、パス区切り (`/`) 文字を含め、URL の生成にルートが使用されるときに適切な文字がエスケープされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-431">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="01b9a-432">たとえば、ルート値が `{ path = "my/path" }` のルート `foo/{*path}` では、`foo/my%2Fpath` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-432">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="01b9a-433">エスケープされたスラッシュに注意してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-433">Note the escaped forward slash.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-434">ルート パラメーターには、*既定値* が含まれることがあります。パラメーター名の後に既定値を指定し、等号 (`=`) で区切ることで指定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-434">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="01b9a-435">たとえば、`{controller=Home}` では、`controller` の既定値として `Home` が定義されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-435">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="01b9a-436">パラメーターの URL に値がない場合、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-436">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="01b9a-437">ルート パラメーターは、`id?` のように、パラメーター名の終わりに疑問符 (`?`) を追加することでオプションとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-437">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="01b9a-438">オプション値と既定ルート パラメーターの違いは、既定値を含むルート パラメーターは常に値を生成するのに対し、オプションのパラメーターには、要求 URL によって値が指定されたときにのみ値が与えられることにあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-438">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="01b9a-439">ルート パラメーターには、URL からバインドされるルート値に一致しなければならないという制約が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-439">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="01b9a-440">コロン (`:`) と制約名をルート パラメーター名の後に追加すると、ルート パラメーターの*インライン制約*が指定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-440">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="01b9a-441">その制約で引数が要求される場合、制約名の後にかっこ (`(...)`) で囲まれます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-441">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="01b9a-442">別のコロン (`:`) と制約名を追加することで、複数のインライン制約を指定できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-442">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="01b9a-443">制約名と引数が <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> サービスに渡され、URL 処理で使用する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-443">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="01b9a-444">たとえば、ルート テンプレート `blog/{article:minlength(10)}` によって、制約 `minlength` と引数 `10` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-444">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="01b9a-445">ルート制約の詳細とこのフレームワークによって指定される制約のリストについては、「[ルート制約参照](#route-constraint-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-445">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="01b9a-446">ルート パラメーターには、リンクを生成し、ページおよびアクションを URL と一致させるときにパラメーターの値を変換する、パラメーター トランスフォーマーが含まれている場合もあります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-446">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="01b9a-447">制約と同様に、パラメーター トランスフォーマーをルート パラメーターにインラインで追加することができます。その場合、ルート パラメーター名の後にコロン (`:`) とトランスフォーマー名を追加します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-447">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="01b9a-448">たとえば、ルート テンプレート `blog/{article:slugify}` では、`slugify` トランスフォーマーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-448">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="01b9a-449">パラメーター トランスフォーマーの詳細については、「[パラメーター トランスフォーマー参照](#parameter-transformer-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-449">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

::: moniker-end

<span data-ttu-id="01b9a-450">次の表は、ルート テンプレートの例とその動作をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-450">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="01b9a-451">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="01b9a-451">Route Template</span></span>                           | <span data-ttu-id="01b9a-452">一致する URI の例</span><span class="sxs-lookup"><span data-stu-id="01b9a-452">Example Matching URI</span></span>    | <span data-ttu-id="01b9a-453">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="01b9a-453">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="01b9a-454">単一パス `/hello` にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-454">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="01b9a-455">一致し、`Page` が `Home` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-455">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="01b9a-456">一致し、`Page` が `Contact` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-456">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="01b9a-457">`Products` コントローラーと `List` アクションにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-457">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="01b9a-458">`Products` コントローラーと `Details` アクションにマッピングされます (`id` は 123 に設定されます)。</span><span class="sxs-lookup"><span data-stu-id="01b9a-458">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="01b9a-459">`Home` コントローラーと `Index` メソッドにマッピングされます (`id` は無視されます)。</span><span class="sxs-lookup"><span data-stu-id="01b9a-459">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="01b9a-460">一般的に、テンプレートの利用が最も簡単なルーティングの手法となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-460">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="01b9a-461">ルート テンプレート以外では、制約と既定値も指定できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-461">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="01b9a-462">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-462">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="01b9a-463">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="01b9a-463">Reserved routing names</span></span>

<span data-ttu-id="01b9a-464">次のキーワードは予約名であり、ルート名やパラメーターとして使用できません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-464">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="01b9a-465">ルート制約参照</span><span class="sxs-lookup"><span data-stu-id="01b9a-465">Route constraint reference</span></span>

<span data-ttu-id="01b9a-466">ルート制約は、受信 URL と一致し、URL パスがルート値にトークン化されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-466">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="01b9a-467">ルート制約では、通常、ルート テンプレート経由で関連付けられるルート値を調べ、値が許容できるかどうかをはい/いいえで決定します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-467">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="01b9a-468">一部のルート制約では、ルート値以外のデータを使用し、要求をルーティングできるかどうかが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-468">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="01b9a-469">たとえば、<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> はその HTTP Verb に基づいて要求を承認または却下します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-469">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="01b9a-470">制約は、要求のルーティングとリンクの生成で使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-470">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="01b9a-471">**入力の検証**には制約を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-471">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="01b9a-472">**入力の検証**に制約が使用されると、無効な入力の結果、*400 - Bad Request* と適切なエラー メッセージではなく、*404 - Not Found* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-472">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="01b9a-473">ルート制約は、特定のルートに対する入力の妥当性を検証するためではなく、似たようなルートの**違いを明らかにする**ために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-473">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="01b9a-474">次の表は、ルート制約の例とそれに求められる動作をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-474">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="01b9a-475">制約</span><span class="sxs-lookup"><span data-stu-id="01b9a-475">constraint</span></span> | <span data-ttu-id="01b9a-476">例</span><span class="sxs-lookup"><span data-stu-id="01b9a-476">Example</span></span> | <span data-ttu-id="01b9a-477">一致の例</span><span class="sxs-lookup"><span data-stu-id="01b9a-477">Example Matches</span></span> | <span data-ttu-id="01b9a-478">メモ</span><span class="sxs-lookup"><span data-stu-id="01b9a-478">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="01b9a-479">`123456789`、 `-123456789`</span><span class="sxs-lookup"><span data-stu-id="01b9a-479">`123456789`, `-123456789`</span></span> | <span data-ttu-id="01b9a-480">あらゆる整数に一致する</span><span class="sxs-lookup"><span data-stu-id="01b9a-480">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="01b9a-481">`true`、 `FALSE`</span><span class="sxs-lookup"><span data-stu-id="01b9a-481">`true`, `FALSE`</span></span> | <span data-ttu-id="01b9a-482">`true` または `false` に一致する (大文字と小文字を区別しません)</span><span class="sxs-lookup"><span data-stu-id="01b9a-482">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="01b9a-483">`2016-12-31`、 `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="01b9a-483">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="01b9a-484">有効な `DateTime` 値に一致する (インバリアント カルチャで - 警告参照)</span><span class="sxs-lookup"><span data-stu-id="01b9a-484">Matches a valid `DateTime` value (in the invariant culture - see warning)</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="01b9a-485">`49.99`、 `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="01b9a-485">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="01b9a-486">有効な `decimal` 値に一致する (インバリアント カルチャで - 警告参照)</span><span class="sxs-lookup"><span data-stu-id="01b9a-486">Matches a valid `decimal` value (in the invariant culture - see warning)</span></span> |
| `double` | `{weight:double}` | <span data-ttu-id="01b9a-487">`1.234`、 `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="01b9a-487">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="01b9a-488">有効な `double` 値に一致する (インバリアント カルチャで - 警告参照)</span><span class="sxs-lookup"><span data-stu-id="01b9a-488">Matches a valid `double` value (in the invariant culture - see warning)</span></span> |
| `float` | `{weight:float}` | <span data-ttu-id="01b9a-489">`1.234`、 `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="01b9a-489">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="01b9a-490">有効な `float` 値に一致する (インバリアント カルチャで - 警告参照)</span><span class="sxs-lookup"><span data-stu-id="01b9a-490">Matches a valid `float` value (in the invariant culture - see warning)</span></span> |
| `guid` | `{id:guid}` | <span data-ttu-id="01b9a-491">`CD2C1638-1638-72D5-1638-DEADBEEF1638`、 `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="01b9a-491">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="01b9a-492">有効な `Guid` 値に一致する</span><span class="sxs-lookup"><span data-stu-id="01b9a-492">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="01b9a-493">`123456789`、 `-123456789`</span><span class="sxs-lookup"><span data-stu-id="01b9a-493">`123456789`, `-123456789`</span></span> | <span data-ttu-id="01b9a-494">有効な `long` 値に一致する</span><span class="sxs-lookup"><span data-stu-id="01b9a-494">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="01b9a-495">4 文字以上の文字列であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-495">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="01b9a-496">8 文字以内の文字列であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-496">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="01b9a-497">厳密に 12 文字の文字列であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-497">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="01b9a-498">8 文字以上、16 文字以内の文字列であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-498">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="01b9a-499">18 以上の整数値であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-499">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="01b9a-500">120 以下の整数値であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-500">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="01b9a-501">18 以上、120 以下の整数値であることが必要</span><span class="sxs-lookup"><span data-stu-id="01b9a-501">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="01b9a-502">1 つまたは複数のアルファベット文字で構成される文字列が必要 (`a`-`z`、大文字と小文字を区別)</span><span class="sxs-lookup"><span data-stu-id="01b9a-502">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="01b9a-503">正規表現に一致する文字列が必要 (正規表現の定義についてはヒントを参照)</span><span class="sxs-lookup"><span data-stu-id="01b9a-503">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="01b9a-504">URL 生成中、非パラメーターが提示されるように強制する</span><span class="sxs-lookup"><span data-stu-id="01b9a-504">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="01b9a-505">コロンで区切られた複数の制約を単一のパラメーターに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-505">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="01b9a-506">たとえば、次の制約では、パラメーターが 1 以上の整数値に制限されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-506">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="01b9a-507">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-507">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="01b9a-508">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-508">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="01b9a-509">フレームワークから提供されるルート制約がルート値に格納されている値を変更することはありません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-509">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="01b9a-510">URL から解析されたルート値はすべて文字列として格納されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-510">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="01b9a-511">たとえば、`float` 制約はルート値を浮動小数に変換しますが、変換された値は、浮動小数に変換できることを検証するためにだけ利用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-511">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="01b9a-512">正規表現</span><span class="sxs-lookup"><span data-stu-id="01b9a-512">Regular expressions</span></span>

<span data-ttu-id="01b9a-513">ASP.NET Core フレームワークでは、正規表現コンストラクターに `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-513">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="01b9a-514">これらのメンバーの詳細については、「<xref:System.Text.RegularExpressions.RegexOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-514">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="01b9a-515">正規表現では、ルーティングや C# 言語で使用されるものに似た区切り記号とトークンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-515">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="01b9a-516">正規表現トークンはエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-516">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="01b9a-517">ルーティングで正規表現 `^\d{3}-\d{2}-\d{4}$` を使用するには、C# ソース ファイルで `\\` (二重円記号) 文字として文字列に `\` (単一の円記号) 文字を指定し、文字列エスケープ文字である `\` をエスケープする必要があります ([逐語的な文字列リテラル](/dotnet/csharp/language-reference/keywords/string)を使用しない限り)。</span><span class="sxs-lookup"><span data-stu-id="01b9a-517">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="01b9a-518">ルーティング パラメーター区切り記号文字 (`{`、`}`、`[`、`]`) をエスケープするには、表現の文字を二重にします (`{{`、`}`、`[[`、`]]`)。</span><span class="sxs-lookup"><span data-stu-id="01b9a-518">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="01b9a-519">次の表に、正規表現とエスケープ適用後のものを示します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-519">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="01b9a-520">正規表現</span><span class="sxs-lookup"><span data-stu-id="01b9a-520">Regular Expression</span></span>    | <span data-ttu-id="01b9a-521">エスケープ適用後の正規表現</span><span class="sxs-lookup"><span data-stu-id="01b9a-521">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="01b9a-522">ルーティングで使用される正規表現は、多くの場合、キャレット (`^`) 文字で始まり、文字列の開始位置と一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-522">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="01b9a-523">表現は、多くの場合、ドル記号 (`$`) で終わり、文字列の末尾と一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-523">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="01b9a-524">`^` 文字と `$` 文字により、正規表現はルート パラメーター値全体に一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-524">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="01b9a-525">`^` 文字と `$` 文字がなければ、正規表現は、文字列内のあらゆる部分文字列に一致します。それは多くの場合、意図に反することです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-525">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="01b9a-526">下の表では例を示し、それらが一致する、または一致しない理由について説明します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-526">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="01b9a-527">正規表現</span><span class="sxs-lookup"><span data-stu-id="01b9a-527">Expression</span></span>   | <span data-ttu-id="01b9a-528">String</span><span class="sxs-lookup"><span data-stu-id="01b9a-528">String</span></span>    | <span data-ttu-id="01b9a-529">一致したもの</span><span class="sxs-lookup"><span data-stu-id="01b9a-529">Match</span></span> | <span data-ttu-id="01b9a-530">コメント</span><span class="sxs-lookup"><span data-stu-id="01b9a-530">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="01b9a-531">hello</span><span class="sxs-lookup"><span data-stu-id="01b9a-531">hello</span></span>     | <span data-ttu-id="01b9a-532">はい</span><span class="sxs-lookup"><span data-stu-id="01b9a-532">Yes</span></span>   | <span data-ttu-id="01b9a-533">サブ文字列の一致</span><span class="sxs-lookup"><span data-stu-id="01b9a-533">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="01b9a-534">123abc456</span><span class="sxs-lookup"><span data-stu-id="01b9a-534">123abc456</span></span> | <span data-ttu-id="01b9a-535">はい</span><span class="sxs-lookup"><span data-stu-id="01b9a-535">Yes</span></span>   | <span data-ttu-id="01b9a-536">サブ文字列の一致</span><span class="sxs-lookup"><span data-stu-id="01b9a-536">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="01b9a-537">mz</span><span class="sxs-lookup"><span data-stu-id="01b9a-537">mz</span></span>        | <span data-ttu-id="01b9a-538">はい</span><span class="sxs-lookup"><span data-stu-id="01b9a-538">Yes</span></span>   | <span data-ttu-id="01b9a-539">一致する表現</span><span class="sxs-lookup"><span data-stu-id="01b9a-539">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="01b9a-540">MZ</span><span class="sxs-lookup"><span data-stu-id="01b9a-540">MZ</span></span>        | <span data-ttu-id="01b9a-541">はい</span><span class="sxs-lookup"><span data-stu-id="01b9a-541">Yes</span></span>   | <span data-ttu-id="01b9a-542">大文字と小文字の使い方が違う</span><span class="sxs-lookup"><span data-stu-id="01b9a-542">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="01b9a-543">hello</span><span class="sxs-lookup"><span data-stu-id="01b9a-543">hello</span></span>     | <span data-ttu-id="01b9a-544">いいえ</span><span class="sxs-lookup"><span data-stu-id="01b9a-544">No</span></span>    | <span data-ttu-id="01b9a-545">上の `^` と `$` を参照</span><span class="sxs-lookup"><span data-stu-id="01b9a-545">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="01b9a-546">123abc456</span><span class="sxs-lookup"><span data-stu-id="01b9a-546">123abc456</span></span> | <span data-ttu-id="01b9a-547">いいえ</span><span class="sxs-lookup"><span data-stu-id="01b9a-547">No</span></span>    | <span data-ttu-id="01b9a-548">上の `^` と `$` を参照</span><span class="sxs-lookup"><span data-stu-id="01b9a-548">See `^` and `$` above</span></span> |

<span data-ttu-id="01b9a-549">正規表現構文の詳細については、[.NET Framework 正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-549">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="01b9a-550">既知の入力可能値の集まりにパラメーターを制限するには、正規表現を使用します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-550">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="01b9a-551">たとえば、`{action:regex(^(list|get|create)$)}` の場合、`action` ルート値は `list`、`get`、`create` とのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-551">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="01b9a-552">制約ディクショナリに渡された場合、文字列 `^(list|get|create)$` で同じものになります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-552">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="01b9a-553">(テンプレート内でインラインではなく) 制約ディクショナリに渡された制約が既知の制約に一致しない場合も、正規表現として扱われます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-553">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="01b9a-554">カスタム ルート制約</span><span class="sxs-lookup"><span data-stu-id="01b9a-554">Custom Route Constraints</span></span>

<span data-ttu-id="01b9a-555">組み込みのルート制約だけでなく、<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスを実装してカスタム ルート制約を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-555">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="01b9a-556"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスには、1 つのメソッド `Match` が含まれています。これは、制約が満たされている場合は `true` を、それ以外の場合は `false` を返します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-556">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="01b9a-557">カスタムの <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> を使うには、アプリのサービス コンテナー内にあるアプリの <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> に、ルート制約の種類を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-557">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="01b9a-558"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> は、ルート制約キーを、その制約を検証する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> の実装にマッピングするディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-558">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="01b9a-559">アプリの <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> は、`Startup.ConfigureServices` で、[services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼び出しの一部として、または `services.Configure<RouteOptions>` を使って <xref:Microsoft.AspNetCore.Routing.RouteOptions> を直接構成することで、更新できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-559">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="01b9a-560">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-560">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="01b9a-561">これで、制約の種類を登録するときに指定した名前を使って、通常の方法でルートに制約を適用できます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-561">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="01b9a-562">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-562">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

::: moniker range=">= aspnetcore-2.2"

## <a name="parameter-transformer-reference"></a><span data-ttu-id="01b9a-563">パラメーター トランスフォーマー参照</span><span class="sxs-lookup"><span data-stu-id="01b9a-563">Parameter transformer reference</span></span>

<span data-ttu-id="01b9a-564">パラメーター トランスフォーマー:</span><span class="sxs-lookup"><span data-stu-id="01b9a-564">Parameter transformers:</span></span>

* <span data-ttu-id="01b9a-565"><xref:Microsoft.AspNetCore.Routing.Route> のリンクの生成時に実行されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-565">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="01b9a-566">`Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`を実装します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-566">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="01b9a-567"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-567">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="01b9a-568">パラメーターのルート値を取得し、それを新しい文字列値に変換します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-568">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="01b9a-569">変換された値は生成されたリンクで使用されるようになります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-569">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="01b9a-570">たとえば、`Url.Action(new { article = "MyTestArticle" })` のルート パターン `blog\{article:slugify}` のカスタム `slugify` パラメーター トランスフォーマーでは、`blog\my-test-article` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-570">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="01b9a-571">ルート パターンでパラメーター トランスフォーマーを使用するには、まず `Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> を使用してこれを構成します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-571">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="01b9a-572">パラメーター トランスフォーマーは、エンドポイントが解決される URI を変換するためにフレームワークで使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-572">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="01b9a-573">たとえば、ASP.NET Core MVC ではパラメーター トランスフォーマーを使用して、`area`、`controller`、`action`、`page` を照合するために使用されるルート値を変換します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-573">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="01b9a-574">上記のルートでは、アクション `SubscriptionManagementController.GetAll()` は URI `/subscription-management/get-all` と一致します。</span><span class="sxs-lookup"><span data-stu-id="01b9a-574">With the preceding route, the action `SubscriptionManagementController.GetAll()` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="01b9a-575">パラメーター トランスフォーマーでは、リンクを生成するために使用されるルート値は変更されません。</span><span class="sxs-lookup"><span data-stu-id="01b9a-575">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="01b9a-576">たとえば、`Url.Action("GetAll", "SubscriptionManagement")` では `/subscription-management/get-all` が出力されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-576">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="01b9a-577">ASP.NET Core では、生成されたルートと共にパラメーター トランスフォーマーを使用するための API 規則が提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-577">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="01b9a-578">ASP.NET Core MVC には、`Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 規則が備わっています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-578">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="01b9a-579">この規則では、指定されたパラメーター トランスフォーマーがアプリ内のすべての属性ルートに適用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-579">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="01b9a-580">パラメーター トランスフォーマーでは、置き換えられる属性ルート トークンが変換されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-580">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="01b9a-581">詳細については、「[パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-581">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="01b9a-582">Razor Pages には、`Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 規則があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-582">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="01b9a-583">この規則では、指定されたパラメーター トランスフォーマーが自動で検出されたすべての Razor Pages に適用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-583">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="01b9a-584">パラメーター トランスフォーマーでは、Razor Pages ルートのフォルダーとファイル名のセグメントが変換されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-584">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="01b9a-585">詳細については、[パラメーター トランスフォーマーを使用したページ ルートのカスタマイズ](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-585">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

::: moniker-end

## <a name="url-generation-reference"></a><span data-ttu-id="01b9a-586">URL 生成参照</span><span class="sxs-lookup"><span data-stu-id="01b9a-586">URL generation reference</span></span>

<span data-ttu-id="01b9a-587">次の例では、ルートのリンクを生成する方法を確認できます。ルート値のディクショナリと <xref:Microsoft.AspNetCore.Routing.RouteCollection> が指定されています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-587">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="01b9a-588">上のサンプルの終わりで生成された <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> は `/package/create/123` です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-588">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="01b9a-589">ディクショナリにより、"Track Package Route" テンプレート `package/{operation}/{id}` のルート値 `operation` と `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-589">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="01b9a-590">詳細については、「[ルーティング ミドルウェアの使用](#use-routing-middleware)」セクションのサンプル コードまたは[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-590">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="01b9a-591"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> コンストラクターの 2 番目のパラメーターは*アンビエント値*の集合です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-591">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="01b9a-592">アンビエント値は、開発者が要求コンテキスト内で指定する必要がある値の数が制限されるため、使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="01b9a-592">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="01b9a-593">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-593">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="01b9a-594">ASP.NET Core MVC アプリの `HomeController` の `About` アクションでは、コントローラー ルート値を指定し、`Index` アクションにリンクする必要はありません。`Home` のアンビエント値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-594">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="01b9a-595">パラメーターに一致しないアンビエント値は無視されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-595">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="01b9a-596">アンビエント値は、明示的に指定された値でアンビエント値がオーバーライドされる場合にも無視されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-596">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="01b9a-597">URL では左から右に照合されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-597">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="01b9a-598">明示的に指定されているが、ルートのセグメントと一致しない値は、クエリ文字列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-598">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="01b9a-599">次の表は、ルート テンプレート `{controller}/{action}/{id?}` の使用時の結果をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="01b9a-599">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="01b9a-600">アンビエント値</span><span class="sxs-lookup"><span data-stu-id="01b9a-600">Ambient Values</span></span>                     | <span data-ttu-id="01b9a-601">明示的な値</span><span class="sxs-lookup"><span data-stu-id="01b9a-601">Explicit Values</span></span>                        | <span data-ttu-id="01b9a-602">結果</span><span class="sxs-lookup"><span data-stu-id="01b9a-602">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="01b9a-603">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="01b9a-603">controller = "Home"</span></span>                | <span data-ttu-id="01b9a-604">action = "About"</span><span class="sxs-lookup"><span data-stu-id="01b9a-604">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="01b9a-605">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="01b9a-605">controller = "Home"</span></span>                | <span data-ttu-id="01b9a-606">controller = "Order", action = "About"</span><span class="sxs-lookup"><span data-stu-id="01b9a-606">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="01b9a-607">controller = "Home", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="01b9a-607">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="01b9a-608">action = "About"</span><span class="sxs-lookup"><span data-stu-id="01b9a-608">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="01b9a-609">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="01b9a-609">controller = "Home"</span></span>                | <span data-ttu-id="01b9a-610">action = "About", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="01b9a-610">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="01b9a-611">パラメーターに対応しない既定値がルートにあり、その値が明示的に指定される場合、それは既定値に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01b9a-611">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="01b9a-612">リンク生成では、`controller` と `action` について一致する値が指定されるときにのみ、このルートのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-612">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="01b9a-613">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="01b9a-613">Complex segments</span></span>

<span data-ttu-id="01b9a-614">複雑なセグメント (例: `[Route("/x{token}y")]`) は、リテラルを右から左に最短一致の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="01b9a-614">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="01b9a-615">複雑なセグメントの一致方法に関する詳しい説明については、[こちらのコード](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="01b9a-615">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="01b9a-616">この[コード サンプル](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)は ASP.NET Core では使われていませんが、複雑なセグメントに関する優れた説明が提供されています。</span><span class="sxs-lookup"><span data-stu-id="01b9a-616">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->
