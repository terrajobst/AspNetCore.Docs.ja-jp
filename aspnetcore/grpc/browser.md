---
title: ブラウザーアプリでの gRPC
author: jamesnk
description: GRPC-Web を使用してブラウザーアプリから呼び出すことができるように、ASP.NET Core で gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830634"
---
# <a name="grpc-in-browser-apps"></a><span data-ttu-id="0cc53-103">ブラウザーアプリでの gRPC</span><span class="sxs-lookup"><span data-stu-id="0cc53-103">gRPC in browser apps</span></span>

<span data-ttu-id="0cc53-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="0cc53-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0cc53-105">**gRPC-.NET での Web サポートは試験段階です**</span><span class="sxs-lookup"><span data-stu-id="0cc53-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="0cc53-106">gRPC-Web for .NET は、コミットされた製品ではなく、試験的なプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="0cc53-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="0cc53-107">次のようにします。</span><span class="sxs-lookup"><span data-stu-id="0cc53-107">We want to:</span></span>
>
> * <span data-ttu-id="0cc53-108">GRPC の実装方法をテストします。</span><span class="sxs-lookup"><span data-stu-id="0cc53-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="0cc53-109">プロキシ経由で gRPC-Web を設定する従来の方法と比較して、.NET 開発者にとってこのアプローチが役立つかどうかについてのフィードバックをお寄せください。</span><span class="sxs-lookup"><span data-stu-id="0cc53-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="0cc53-110">[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)でフィードバックを残して、開発者が、で生産性を向上させることができることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="0cc53-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="0cc53-111">ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="0cc53-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="0cc53-112">[grpc-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)は、ブラウザーの JavaScript および Blazor アプリが grpc サービスを呼び出すことができるようにするプロトコルです。</span><span class="sxs-lookup"><span data-stu-id="0cc53-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="0cc53-113">この記事では、.NET Core で gRPC-Web を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="0cc53-114">ASP.NET Core での gRPC-Web の構成</span><span class="sxs-lookup"><span data-stu-id="0cc53-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="0cc53-115">ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="0cc53-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="0cc53-116">gRPC-Web では、サービスを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0cc53-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="0cc53-117">唯一の変更はスタートアップ構成です。</span><span class="sxs-lookup"><span data-stu-id="0cc53-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="0cc53-118">ASP.NET Core gRPC サービスを使用して gRPC-Web を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="0cc53-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="0cc53-119">[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Web)パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="0cc53-120">`AddGrpcWeb` と `UseGrpcWeb` を*Startup.cs*に追加して grpc-Web を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

<span data-ttu-id="0cc53-121">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0cc53-121">The preceding code:</span></span>

* <span data-ttu-id="0cc53-122">GRPC-Web ミドルウェア、`UseGrpcWeb`、およびエンドポイントの前に、を追加します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="0cc53-123">`endpoints.MapGrpcService<GreeterService>()` メソッドが `EnableGrpcWeb`で gRPC-Web をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="0cc53-124">または、ConfigureServices に `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` を追加して、gRPC-Web をサポートするようにすべてのサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

<span data-ttu-id="0cc53-125">CORS をサポートするように ASP.NET Core を構成するなど、ブラウザーから gRPC-Web を呼び出すには、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0cc53-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="0cc53-126">詳細については、「 [CORS のサポート](xref:security/cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cc53-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="0cc53-127">ブラウザーから gRPC-Web を呼び出す</span><span class="sxs-lookup"><span data-stu-id="0cc53-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="0cc53-128">ブラウザーアプリは gRPC-Web を使用して gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="0cc53-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="0cc53-129">ブラウザーから gRPC-Web で gRPC サービスを呼び出すときには、いくつかの要件と制限があります。</span><span class="sxs-lookup"><span data-stu-id="0cc53-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="0cc53-130">サーバーは、gRPC-Web をサポートするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cc53-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="0cc53-131">クライアントストリーミングと双方向のストリーミング呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0cc53-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="0cc53-132">サーバーストリーミングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0cc53-132">Server streaming is supported.</span></span>
* <span data-ttu-id="0cc53-133">別のドメインで gRPC サービスを呼び出すには、サーバーで[CORS](xref:security/cors)を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cc53-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="0cc53-134">JavaScript gRPC-Web クライアント</span><span class="sxs-lookup"><span data-stu-id="0cc53-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="0cc53-135">JavaScript gRPC-Web クライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="0cc53-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="0cc53-136">JavaScript から gRPC-Web を使用する方法については、「 [grpc-web を使用して javascript クライアントコードを記述](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0cc53-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="0cc53-137">.NET gRPC クライアントを使用して gRPC-Web を構成する</span><span class="sxs-lookup"><span data-stu-id="0cc53-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="0cc53-138">.NET gRPC クライアントは、gRPC-Web 呼び出しを行うように構成できます。</span><span class="sxs-lookup"><span data-stu-id="0cc53-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="0cc53-139">これは、ブラウザーでホストされ、JavaScript コードの HTTP 制限が同じである[Blazor WebAssembly](xref:blazor/index#blazor-webassembly)に便利です。</span><span class="sxs-lookup"><span data-stu-id="0cc53-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="0cc53-140">.NET クライアントを使用した gRPC の呼び出しは[、HTTP/2 gRPC と同じ](xref:grpc/client)です。</span><span class="sxs-lookup"><span data-stu-id="0cc53-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="0cc53-141">唯一の変更は、チャネルの作成方法です。</span><span class="sxs-lookup"><span data-stu-id="0cc53-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="0cc53-142">GRPC-Web を使用するには:</span><span class="sxs-lookup"><span data-stu-id="0cc53-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="0cc53-143">[Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="0cc53-144">`GrpcWebHandler`を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-144">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="0cc53-145">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0cc53-145">The preceding code:</span></span>

* <span data-ttu-id="0cc53-146">GRPC-Web を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-146">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="0cc53-147">クライアントを作成し、チャネルを使用して呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="0cc53-147">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="0cc53-148">`GrpcWebHandler` の作成時には、次の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="0cc53-148">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="0cc53-149">**Innerhandler**: HTTP 呼び出しを行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler`など)。</span><span class="sxs-lookup"><span data-stu-id="0cc53-149">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the HTTP call, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="0cc53-150">**Mode**: `GrpcWebMode` 列挙型。</span><span class="sxs-lookup"><span data-stu-id="0cc53-150">**Mode**: `GrpcWebMode` enum.</span></span> <span data-ttu-id="0cc53-151">`GrpcWebMode.GrpcWebText` は、サーバーストリーミング呼び出しをサポートするために必要なコンテンツを base64 でエンコードするように構成します。</span><span class="sxs-lookup"><span data-stu-id="0cc53-151">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded, which is required to support server streaming calls.</span></span>
* <span data-ttu-id="0cc53-152">**HttpVersion**: HTTP プロトコル `Version`。</span><span class="sxs-lookup"><span data-stu-id="0cc53-152">**HttpVersion**: HTTP protocol `Version`.</span></span> <span data-ttu-id="0cc53-153">gRPC-Web は特定のプロトコルを必要とせず、構成されていない限り、要求を行うときには指定しません。</span><span class="sxs-lookup"><span data-stu-id="0cc53-153">gRPC-Web doesn't require a specific protocol and won't specify one when making a request unless configured.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cc53-154">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0cc53-154">Additional resources</span></span>

* [<span data-ttu-id="0cc53-155">Web クライアント用 gRPC GitHub プロジェクト</span><span class="sxs-lookup"><span data-stu-id="0cc53-155">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
