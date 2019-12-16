---
title: ASP.NET Core 2.2 の新機能
author: rick-anderson
description: ASP.NET Core 2.2 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.2
ms.openlocfilehash: 8995a514ea2e5016da85952d0f0beaf396a5d639
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880843"
---
# <a name="whats-new-in-aspnet-core-22"></a><span data-ttu-id="8a754-103">ASP.NET Core 2.2 の新機能</span><span class="sxs-lookup"><span data-stu-id="8a754-103">What's new in ASP.NET Core 2.2</span></span>

<span data-ttu-id="8a754-104">この記事では、ASP.NET Core 2.2 の最も大きな変更点について説明します。また、その変更点のドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="8a754-104">This article highlights the most significant changes in ASP.NET Core 2.2, with links to relevant documentation.</span></span>

## <a name="openapi-analyzers--conventions"></a><span data-ttu-id="8a754-105">OpenAPI のアナライザーと規則</span><span class="sxs-lookup"><span data-stu-id="8a754-105">OpenAPI Analyzers & Conventions</span></span>

<span data-ttu-id="8a754-106">OpenAPI (旧称 Swagger) は、REST API を記述するために、言語に関係なく使用できる仕様です。</span><span class="sxs-lookup"><span data-stu-id="8a754-106">OpenAPI (formerly known as Swagger) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="8a754-107">OpenAPI エコシステムには、この仕様を使用してクライアント コードの検出、テスト、および作成を行うことができるツールがあります。</span><span class="sxs-lookup"><span data-stu-id="8a754-107">The OpenAPI ecosystem has tools that allow for discovering, testing, and producing client code using the specification.</span></span> <span data-ttu-id="8a754-108">ASP.NET Core MVC での OpenAPI ドキュメントの生成と視覚化のサポートは、[NSwag](https://github.com/RicoSuter/NSwag) や [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) などのコミュニティ主導のプロジェクトを介して提供されています。</span><span class="sxs-lookup"><span data-stu-id="8a754-108">Support for generating and visualizing OpenAPI documents in ASP.NET Core MVC is provided via community driven projects such as [NSwag](https://github.com/RicoSuter/NSwag) and [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="8a754-109">ASP.NET Core 2.2 では、OpenAPI ドキュメントを作成するためのツールとランタイム エクスペリエンスが改善されています。</span><span class="sxs-lookup"><span data-stu-id="8a754-109">ASP.NET Core 2.2 provides improved tooling and runtime experiences for creating OpenAPI documents.</span></span>

<span data-ttu-id="8a754-110">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-110">For more information, see the following resources:</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [<span data-ttu-id="8a754-111">ASP.NET Core 2.2.0-preview1:OpenAPI のアナライザーと規則</span><span class="sxs-lookup"><span data-stu-id="8a754-111">ASP.NET Core 2.2.0-preview1: OpenAPI Analyzers & Conventions</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a><span data-ttu-id="8a754-112">問題の詳細のサポート</span><span class="sxs-lookup"><span data-stu-id="8a754-112">Problem details support</span></span>

<span data-ttu-id="8a754-113">ASP.NET Core 2.1 では、HTTP 応答でエラーの詳細を伝達するための [RFC 7807](https://tools.ietf.org/html/rfc7807) 仕様に基づいて、`ProblemDetails` が導入されました。</span><span class="sxs-lookup"><span data-stu-id="8a754-113">ASP.NET Core 2.1 introduced `ProblemDetails`, based on the [RFC 7807](https://tools.ietf.org/html/rfc7807) specification for carrying details of an error with an HTTP Response.</span></span> <span data-ttu-id="8a754-114">2\.2 で、`ProblemDetails` は、属性が `ApiControllerAttribute` のコントローラーのクライアント エラー コードに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="8a754-114">In 2.2, `ProblemDetails` is the standard response for client error codes in controllers attributed with `ApiControllerAttribute`.</span></span> <span data-ttu-id="8a754-115">クライアント エラー状態コード (4xx) を返している `IActionResult` は、`ProblemDetails` の本文を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="8a754-115">An `IActionResult` returning a client error status code (4xx) now returns a `ProblemDetails` body.</span></span> <span data-ttu-id="8a754-116">この結果には、要求ログを使用してエラーを関連付けるために使用できる相関関係 ID も含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a754-116">The result also includes a correlation ID that can be used to correlate the error using request logs.</span></span> <span data-ttu-id="8a754-117">クライアント エラーの場合、`ProducesResponseType` の既定では `ProblemDetails` が応答の種類として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a754-117">For client errors, `ProducesResponseType` defaults to using `ProblemDetails` as the response type.</span></span> <span data-ttu-id="8a754-118">これは、NSwag または Swashbuckle.AspNetCore を使用して生成される OpenAPI/Swagger 出力にドキュメント化されます。</span><span class="sxs-lookup"><span data-stu-id="8a754-118">This is documented in OpenAPI / Swagger output generated using NSwag or Swashbuckle.AspNetCore.</span></span>

## <a name="endpoint-routing"></a><span data-ttu-id="8a754-119">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="8a754-119">Endpoint Routing</span></span>

<span data-ttu-id="8a754-120">ASP.NET Core 2.2 では、要求のディスパッチを改善するために新しい*エンドポイント ルーティング* システムを使用しています。</span><span class="sxs-lookup"><span data-stu-id="8a754-120">ASP.NET Core 2.2 uses a new *endpoint routing* system for improved dispatching of requests.</span></span> <span data-ttu-id="8a754-121">この変更には、新しいリンク生成 API メンバーとルート パラメーター変換機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a754-121">The changes include new link generation API members and route parameter transformers.</span></span>

<span data-ttu-id="8a754-122">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-122">For more information, see the following resources:</span></span>

* [<span data-ttu-id="8a754-123">2.2 のエンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="8a754-123">Endpoint routing in 2.2</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* <span data-ttu-id="8a754-124">[ルート パラメーターの変換機能](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (**ルーティング**に関するセクションを参照してください)</span><span class="sxs-lookup"><span data-stu-id="8a754-124">[Route parameter transformers](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (see **Routing** section)</span></span>
* [<span data-ttu-id="8a754-125">IRouter ベースのルーティングとエンドポイントベースのルーティングの違い</span><span class="sxs-lookup"><span data-stu-id="8a754-125">Differences between IRouter- and endpoint-based routing</span></span>](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a><span data-ttu-id="8a754-126">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="8a754-126">Health checks</span></span>

<span data-ttu-id="8a754-127">新しい正常性チェック サービスを使用すると、Kubernetes などの正常性チェックが必要な環境で ASP.NET Core を簡単に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a754-127">A new health checks service makes it easier to use ASP.NET Core in environments that require health checks, such as Kubernetes.</span></span> <span data-ttu-id="8a754-128">正常性チェックには、ミドルウェアと、`IHealthCheck` の抽象化とサービスを定義する一連のライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a754-128">Health checks includes middleware and a set of libraries that define an `IHealthCheck` abstraction and service.</span></span>

<span data-ttu-id="8a754-129">正常性チェックは、システムが正常に要求に応答しているかどうかを迅速に判断するために、コンテナー オーケストレーターまたはロード バランサーによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a754-129">Health checks are used by a container orchestrator or load balancer to quickly determine if a system is responding to requests normally.</span></span> <span data-ttu-id="8a754-130">正常性に問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。</span><span class="sxs-lookup"><span data-stu-id="8a754-130">A container orchestrator might respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="8a754-131">ロード バランサーは、正常性チェックに対して、障害が発生したサービスのインスタンスからトラフィックをルーティングすることで応答する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a754-131">A load balancer might respond to a health check by routing traffic away from the failing instance of the service.</span></span>

<span data-ttu-id="8a754-132">正常性チェックは、監視システムから使用される HTTP エンドポイントとして、アプリケーションによって公開されています。</span><span class="sxs-lookup"><span data-stu-id="8a754-132">Health checks are exposed by an application as an HTTP endpoint used by monitoring systems.</span></span> <span data-ttu-id="8a754-133">正常性チェックは、さまざまなリアルタイム監視シナリオや監視システムに合わせて構成できます。</span><span class="sxs-lookup"><span data-stu-id="8a754-133">Health checks can be configured for a variety of real-time monitoring scenarios and monitoring systems.</span></span> <span data-ttu-id="8a754-134">正常性チェック サービスは、[BeatPulse プロジェクト](https://github.com/Xabaril/BeatPulse)と統合されています。</span><span class="sxs-lookup"><span data-stu-id="8a754-134">The health checks service integrates with the [BeatPulse project](https://github.com/Xabaril/BeatPulse).</span></span> <span data-ttu-id="8a754-135">そのため、数多くの一般的なシステムや依存関係のチェックを簡単に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="8a754-135">which makes it easier to add checks for dozens of popular systems and dependencies.</span></span>

<span data-ttu-id="8a754-136">詳細については、「[Health checks in ASP.NET Core](xref:host-and-deploy/health-checks)」(ASP.NET Core の正常性チェック) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-136">For more information, see [Health checks in ASP.NET Core](xref:host-and-deploy/health-checks).</span></span>

## <a name="http2-in-kestrel"></a><span data-ttu-id="8a754-137">Kestrel の HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8a754-137">HTTP/2 in Kestrel</span></span>

<span data-ttu-id="8a754-138">ASP.NET Core 2.2 では HTTP/2 のサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="8a754-138">ASP.NET Core 2.2 adds support for HTTP/2.</span></span>

<span data-ttu-id="8a754-139">HTTP/2 は HTTP プロトコルのメジャー リビジョンです。</span><span class="sxs-lookup"><span data-stu-id="8a754-139">HTTP/2 is a major revision of the HTTP protocol.</span></span> <span data-ttu-id="8a754-140">HTTP/2 には次のような注目すべき機能があります。</span><span class="sxs-lookup"><span data-stu-id="8a754-140">Notable features of HTTP/2 include:</span></span>

* <span data-ttu-id="8a754-141">ヘッダーの圧縮をサポート。</span><span class="sxs-lookup"><span data-stu-id="8a754-141">Support for header compression.</span></span>
* <span data-ttu-id="8a754-142">単一の接続での完全に多重化されたストリーム。</span><span class="sxs-lookup"><span data-stu-id="8a754-142">Fully multiplexed streams over a single connection.</span></span>

<span data-ttu-id="8a754-143">HTTP/2 は HTTP のセマンティクス (HTTP ヘッダー、メソッドなど) を維持していますが、データのフレーム化方法やクライアントとサーバー間での送信方法に関して、HTTP/1.x からの破壊的変更があります。</span><span class="sxs-lookup"><span data-stu-id="8a754-143">While HTTP/2 preserves HTTP's semantics (for example, HTTP headers and methods), it's a breaking change from HTTP/1.x on how data is framed and sent between the client and server.</span></span>

<span data-ttu-id="8a754-144">フレーム化に関するこの変更の結果、サーバーとクライアントは、使用されているプロトコル バージョンのネゴシエートが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="8a754-144">As a consequence of this change in framing, servers and clients need to negotiate the protocol version used.</span></span> <span data-ttu-id="8a754-145">アプリケーション層プロトコル ネゴシエーション (ALPN) は、TLS の拡張機能です。これにより、サーバーとクライアントは、その TLS のハンドシェイクの一部として使用されているプロトコルのバージョンをネゴシエートすることができます。</span><span class="sxs-lookup"><span data-stu-id="8a754-145">Application-Layer Protocol Negotiation (ALPN) is a TLS extension that allows the server and client to negotiate the protocol version used as part of their TLS handshake.</span></span> <span data-ttu-id="8a754-146">プロトコルに関してサーバーとクライアント間に事前の情報を持たせることはできますが、すべての主要なブラウザーは、HTTP/2 接続を確立する唯一の方法として ALPN をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8a754-146">While it is possible to have prior knowledge between the server and the client on the protocol, all major browsers support ALPN as the only way to establish an HTTP/2 connection.</span></span>

<span data-ttu-id="8a754-147">詳細については、「[HTTP/2 のサポート](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-147">For more information, see [HTTP/2 support](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).</span></span>

## <a name="kestrel-configuration"></a><span data-ttu-id="8a754-148">Kestrel の構成</span><span class="sxs-lookup"><span data-stu-id="8a754-148">Kestrel configuration</span></span>

<span data-ttu-id="8a754-149">以前のバージョンの ASP.NET Core では、`UseKestrel` を呼び出して Kestrel のオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="8a754-149">In earlier versions of ASP.NET Core, Kestrel options are configured by calling `UseKestrel`.</span></span> <span data-ttu-id="8a754-150">2\.2 で Kestrel のオプションを構成するには、ホスト ビルダー上で `ConfigureKestrel` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a754-150">In 2.2, Kestrel options are configured by calling `ConfigureKestrel` on the host builder.</span></span> <span data-ttu-id="8a754-151">この変更により、インプロセス ホスティングの `IServer` の登録順に関する問題が解決されます。</span><span class="sxs-lookup"><span data-stu-id="8a754-151">This change resolves an issue with the order of `IServer` registrations for in-process hosting.</span></span> <span data-ttu-id="8a754-152">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-152">For more information, see the following resources:</span></span>

* [<span data-ttu-id="8a754-153">UseIIS の競合を軽減する</span><span class="sxs-lookup"><span data-stu-id="8a754-153">Mitigate UseIIS conflict</span></span>](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [<span data-ttu-id="8a754-154">ConfigureKestrel を使用して Kestrel サーバーのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="8a754-154">Configure Kestrel server options with ConfigureKestrel</span></span>](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a><span data-ttu-id="8a754-155">IIS のインプロセス ホスティング</span><span class="sxs-lookup"><span data-stu-id="8a754-155">IIS in-process hosting</span></span>

<span data-ttu-id="8a754-156">以前のバージョンの ASP.NET Core では、IIS はリバース プロキシとして機能しています。</span><span class="sxs-lookup"><span data-stu-id="8a754-156">In earlier versions of ASP.NET Core, IIS serves as a reverse proxy.</span></span> <span data-ttu-id="8a754-157">2\.2 では、ASP.NET Core モジュールで CoreCLR を起動し、IIS worker プロセス (*w3wp.exe*) 内でアプリをホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="8a754-157">In 2.2, the ASP.NET Core Module can boot the CoreCLR and host an app inside the IIS worker process (*w3wp.exe*).</span></span> <span data-ttu-id="8a754-158">インプロセス ホスティングを IIS で実行すると、パフォーマンスと診断機能が向上します。</span><span class="sxs-lookup"><span data-stu-id="8a754-158">In-process hosting provides performance and diagnostic gains when running with IIS.</span></span>

<span data-ttu-id="8a754-159">詳細については、[IIS のインプロセス ホスティング](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-159">For more information, see [in-process hosting for IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).</span></span>

## <a name="opno-locsignalr-java-client"></a>SignalR<span data-ttu-id="8a754-160"> Java クライアント</span><span class="sxs-lookup"><span data-stu-id="8a754-160"> Java client</span></span>

<span data-ttu-id="8a754-161">ASP.NET Core 2.2 には SignalR 用の Java クライアントが導入されました。</span><span class="sxs-lookup"><span data-stu-id="8a754-161">ASP.NET Core 2.2 introduces a Java Client for SignalR.</span></span> <span data-ttu-id="8a754-162">このクライアントは、Android アプリを含め、Java コードから ASP.NET Core SignalR サーバーへの接続をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8a754-162">This client supports connecting to an ASP.NET Core SignalR Server from Java code, including Android apps.</span></span>

<span data-ttu-id="8a754-163">詳細については、「[ASP.NET Core SignalR Java クライアント](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-163">For more information, see [ASP.NET Core SignalR Java client](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).</span></span>

## <a name="cors-improvements"></a><span data-ttu-id="8a754-164">CORS の機能強化</span><span class="sxs-lookup"><span data-stu-id="8a754-164">CORS improvements</span></span>

<span data-ttu-id="8a754-165">以前のバージョンの ASP.NET Core では、`CorsPolicy.Headers` に構成されている値に関係なく、CORS ミドルウェアから `Accept`、`Accept-Language`、`Content-Language`、`Origin` ヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="8a754-165">In earlier versions of ASP.NET Core, CORS Middleware allows `Accept`, `Accept-Language`, `Content-Language`, and `Origin` headers to be sent regardless of the values configured in `CorsPolicy.Headers`.</span></span> <span data-ttu-id="8a754-166">2\.2 では、CORS ミドルウェア ポリシーの一致は、`Access-Control-Request-Headers` で送信されたヘッダーが `WithHeaders` に示されているヘッダーと正確に一致する場合にのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="8a754-166">In 2.2, a CORS Middleware policy match is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="8a754-167">詳細については、[CORS ミドルウェア](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-167">For more information, see [CORS Middleware](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).</span></span>

## <a name="response-compression"></a><span data-ttu-id="8a754-168">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="8a754-168">Response compression</span></span>

<span data-ttu-id="8a754-169">ASP.NET Core 2.2 は、[Brotli 圧縮形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="8a754-169">ASP.NET Core 2.2 can compress responses with the [Brotli compression format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="8a754-170">詳細については、[応答圧縮ミドルウェアによる Brotli 圧縮のサポート](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-170">For more information, see [Response Compression Middleware supports Brotli compression](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).</span></span>

## <a name="project-templates"></a><span data-ttu-id="8a754-171">プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="8a754-171">Project templates</span></span>

<span data-ttu-id="8a754-172">ASP.NET Core Web プロジェクト テンプレートは、[Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) と [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4) に更新されました。</span><span class="sxs-lookup"><span data-stu-id="8a754-172">ASP.NET Core web project templates were updated to [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) and [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4).</span></span> <span data-ttu-id="8a754-173">新しい外観は視覚的にシンプルになり、アプリの重要な構造を簡単に把握できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8a754-173">The new look is visually simpler and makes it easier to see the important structures of the app.</span></span>

![ホームまたはインデックス ページ](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a><span data-ttu-id="8a754-175">検証のパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="8a754-175">Validation performance</span></span>

<span data-ttu-id="8a754-176">MVC の検証システムは、拡張性と柔軟性を考慮して設計されており、特定のモデルにどの検証コントロールが適用されるかを要求単位で判断することができます。</span><span class="sxs-lookup"><span data-stu-id="8a754-176">MVC’s validation system is designed to be extensible and flexible, allowing you to determine on a per request basis which validators apply to a given model.</span></span> <span data-ttu-id="8a754-177">そのため、複雑な検証プロバイダーを作成する場合に最適です。</span><span class="sxs-lookup"><span data-stu-id="8a754-177">This is great for authoring complex validation providers.</span></span> <span data-ttu-id="8a754-178">ただし、最も一般的なケースでは、アプリケーションは組み込みの検証コントロールのみを使用し、このような追加の柔軟性は不要です。</span><span class="sxs-lookup"><span data-stu-id="8a754-178">However, in the most common case an application only uses the built-in validators and don’t require this extra flexibility.</span></span> <span data-ttu-id="8a754-179">組み込みの検証コントロールには、[Required]、[StringLength] などの DataAnnotations や、`IValidatableObject` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a754-179">Built-in validators include DataAnnotations such as [Required] and [StringLength], and `IValidatableObject`.</span></span>

<span data-ttu-id="8a754-180">ASP.NET Core 2.2 では、特定のモデル グラフに検証が不要と判断された場合、MVC で検証を省略することができます。</span><span class="sxs-lookup"><span data-stu-id="8a754-180">In ASP.NET Core 2.2, MVC can short-circuit validation if it determines that a given model graph doesn't require validation.</span></span> <span data-ttu-id="8a754-181">検証をスキップすると、検証モデルに検証コントロールを含めることができない場合や含めない場合に大幅に改善されます。</span><span class="sxs-lookup"><span data-stu-id="8a754-181">Skipping validation results in significant improvements when validating models that can't or don't have any validators.</span></span> <span data-ttu-id="8a754-182">これには、プリミティブ型のコレクション (`byte[]`、`string[]`、`Dictionary<string, string>` など) のオブジェクトや、多数の検証コントロールがない複雑なオブジェクト グラフが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a754-182">This includes objects such as collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`), or complex object graphs without many validators.</span></span>

## <a name="http-client-performance"></a><span data-ttu-id="8a754-183">HTTP クライアントのパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="8a754-183">HTTP Client performance</span></span>

<span data-ttu-id="8a754-184">ASP.NET Core 2.2 では、接続プールのロック競合を減らすことで、`SocketsHttpHandler` のパフォーマンスが向上しました。</span><span class="sxs-lookup"><span data-stu-id="8a754-184">In ASP.NET Core 2.2, the performance of `SocketsHttpHandler` was improved by reducing connection pool locking contention.</span></span> <span data-ttu-id="8a754-185">一部のマイクロサービス アーキテクチャなど、多くの送信 HTTP 要求を作成するアプリの場合、スループットが向上します。</span><span class="sxs-lookup"><span data-stu-id="8a754-185">For apps that make many outgoing HTTP requests, such as some microservices architectures, throughput is improved.</span></span> <span data-ttu-id="8a754-186">読み込み時には、`HttpClient` のスループットが Linux では最大 60%、Windows では 20% 向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a754-186">Under load, `HttpClient` throughput can be improved by up to 60% on Linux and 20% on Windows.</span></span>

<span data-ttu-id="8a754-187">詳細については、[このような改善を実現したプル要求](https://github.com/dotnet/corefx/pull/32568)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-187">For more information, see [the pull request that made this improvement](https://github.com/dotnet/corefx/pull/32568).</span></span>

## <a name="additional-information"></a><span data-ttu-id="8a754-188">追加情報</span><span class="sxs-lookup"><span data-stu-id="8a754-188">Additional information</span></span>

<span data-ttu-id="8a754-189">変更の全一覧については、[ASP.NET Core 2.2 のリリース ノート](https://github.com/aspnet/Home/releases/tag/2.2.0)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a754-189">For the complete list of changes, see the [ASP.NET Core 2.2 Release Notes](https://github.com/aspnet/Home/releases/tag/2.2.0).</span></span>
