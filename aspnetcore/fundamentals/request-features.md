---
title: ASP.NET Core での要求機能
author: ardalis
description: ASP.NET Core のインターフェイスに定義されている HTTP 要求と応答に関連する Web サーバーの実装に関する詳細を学習します。
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416174"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="40070-103">ASP.NET Core での要求機能</span><span class="sxs-lookup"><span data-stu-id="40070-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="40070-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="40070-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="40070-105">HTTP の要求と応答に関連する Web サーバーの実装の詳細が、インターフェイスで定義されています。</span><span class="sxs-lookup"><span data-stu-id="40070-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="40070-106">これらのインターフェイスは、アプリケーションのホスティング パイプラインを作成および変更するために、サーバーの実装とミドルウェアで使用されます。</span><span class="sxs-lookup"><span data-stu-id="40070-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="40070-107">機能インターフェイス</span><span class="sxs-lookup"><span data-stu-id="40070-107">Feature interfaces</span></span>

<span data-ttu-id="40070-108">ASP.NET Core には、サーバーがサポートする機能を識別するために使用される `Microsoft.AspNetCore.Http.Features` に、多数の HTTP 機能インターフェイスが定義されています。</span><span class="sxs-lookup"><span data-stu-id="40070-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="40070-109">次の機能インターフェイスにより、要求が処理され、応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="40070-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="40070-110">`IHttpRequestFeature` プロトコル、パス、クエリ文字列、ヘッダーおよび本文などの HTTP 要求の構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="40070-111">`IHttpResponseFeature` 状態コード、ヘッダー、応答の本文などの HTTP 応答の構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="40070-112">`IHttpAuthenticationFeature``ClaimsPrincipal` に基づいてユーザーを識別し、認証ハンドラーを指定するサポートを定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="40070-113">`IHttpUpgradeFeature`[HTTP アップグレード](https://tools.ietf.org/html/rfc2616.html#section-14.42)のサポートを定義します。これにより、サーバーでプロトコルを切り替えたい場合、使用するその他のプロトコルを指定することが可能になります。</span><span class="sxs-lookup"><span data-stu-id="40070-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="40070-114">`IHttpBufferingFeature` 要求および応答のバッファーを無効化する方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="40070-115">`IHttpConnectionFeature` ローカルおよびリモート アドレスおよびポートのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="40070-116">`IHttpRequestLifetimeFeature` 接続の中止、クライアントの切断により要求が途中で中止されたかどうかの検出のサポートを定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="40070-117">`IHttpSendFileFeature` ファイルを非同期的に送信するためのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="40070-118">`IHttpWebSocketFeature` Web ソケットをサポートする API を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="40070-119">`IHttpRequestIdentifierFeature` 要求を一意に識別するために実装できるプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="40070-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="40070-120">`ISessionFeature` ユーザー セッションをサポートする `ISessionFactory` と `ISession` の抽象化を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="40070-121">`ITlsConnectionFeature` クライアント証明書を取得する API を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="40070-122">`ITlsTokenBindingFeature` TLS トークンのバインド パラメーターを使用するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="40070-123">`ISessionFeature` はサーバー機能ではありませんが、`SessionMiddleware` によって実装されています (「[Managing Application State](app-state.md)」 (アプリケーションの状態の管理) を参照)。</span><span class="sxs-lookup"><span data-stu-id="40070-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="40070-124">機能のコレクション</span><span class="sxs-lookup"><span data-stu-id="40070-124">Feature collections</span></span>

<span data-ttu-id="40070-125">`Features` の `HttpContext` プロパティは、現在の要求で利用可能な HTTP 機能を取得および設定するためのインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="40070-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="40070-126">機能のコレクションは要求のコンテキスト内でも変更可能であるため、コレクションの変更と、その他の機能のサポートの追加にはミドルウェアを使用できます。</span><span class="sxs-lookup"><span data-stu-id="40070-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="40070-127">ミドルウェアおよび要求機能</span><span class="sxs-lookup"><span data-stu-id="40070-127">Middleware and request features</span></span>

<span data-ttu-id="40070-128">サーバーは、機能コレクションの作成を行い、ミドルウェアはこのコレクションの追加と、このコレクションの機能の使用の両方を行います。</span><span class="sxs-lookup"><span data-stu-id="40070-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="40070-129">たとえば、`StaticFileMiddleware` は、`IHttpSendFileFeature` 機能にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="40070-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="40070-130">この機能が存在する場合、これは、その物理パスから、要求された静的ファイルを送信するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="40070-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="40070-131">ない場合、代わりの低速な方法でファイルを送信します。</span><span class="sxs-lookup"><span data-stu-id="40070-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="40070-132">使用可能な場合、`IHttpSendFileFeature` はオペレーティング システムがファイルを開き、ネットワーク カードに直接カーネル モード コピーを実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="40070-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="40070-133">さらに、ミドルウェアは、サーバーによって確立された機能コレクションに追加できます。</span><span class="sxs-lookup"><span data-stu-id="40070-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="40070-134">ミドルウェアでは、既存の機能の代わりをすることも可能で、サーバーの機能を補うことができます。</span><span class="sxs-lookup"><span data-stu-id="40070-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="40070-135">コレクションに追加された機能は、その他のミドルウェアまたは基礎となっているアプリケーション自体で後で要求パイプラインで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="40070-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="40070-136">サーバーのカスタムの実装と特定のミドルウェアの機能強化を組み合わせることにより、アプリケーションで必要な正確な機能セットを構築できます。</span><span class="sxs-lookup"><span data-stu-id="40070-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="40070-137">これにより、サーバーを変更せずに不足している機能を追加できます。また、最小限の機能を公開することにより、外部から攻撃を受ける可能性を減らして、パフォーマンスを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="40070-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="40070-138">まとめ</span><span class="sxs-lookup"><span data-stu-id="40070-138">Summary</span></span>

<span data-ttu-id="40070-139">機能インターフェイスは、特定の要求がサポートする可能性がある特定の HTTP 機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="40070-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="40070-140">サーバーでは、機能のコレクションとそのサーバーによってサポートされる機能の初期セットを定義しますが、ミドルウェアは、これらの機能を強化するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="40070-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40070-141">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="40070-141">Additional resources</span></span>

* [<span data-ttu-id="40070-142">サーバー</span><span class="sxs-lookup"><span data-stu-id="40070-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="40070-143">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="40070-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="40070-144">Open Web Interface for .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="40070-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
