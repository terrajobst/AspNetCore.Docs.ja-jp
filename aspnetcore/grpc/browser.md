---
title: ブラウザー アプリでの gRPC の使用
author: jamesnk
description: GRPC-Web を使用してブラウザーアプリから呼び出すことができるように、ASP.NET Core で gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172270"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="2c8fa-103">ブラウザー アプリでの gRPC の使用</span><span class="sxs-lookup"><span data-stu-id="2c8fa-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="2c8fa-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="2c8fa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2c8fa-105">**gRPC-.NET での Web サポートは試験段階です**</span><span class="sxs-lookup"><span data-stu-id="2c8fa-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="2c8fa-106">gRPC-Web for .NET は、コミットされた製品ではなく、試験的なプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="2c8fa-107">次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-107">We want to:</span></span>
>
> * <span data-ttu-id="2c8fa-108">GRPC の実装方法をテストします。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="2c8fa-109">プロキシ経由で gRPC-Web を設定する従来の方法と比較して、.NET 開発者にとってこのアプローチが役立つかどうかについてのフィードバックをお寄せください。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="2c8fa-110">[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)でフィードバックを残して、開発者が、で生産性を向上させることができることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="2c8fa-111">ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="2c8fa-112">[grpc-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)は、ブラウザーの JavaScript および Blazor アプリが grpc サービスを呼び出すことができるようにするプロトコルです。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="2c8fa-113">この記事では、.NET Core で gRPC-Web を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="2c8fa-114">ASP.NET Core での gRPC-Web の構成</span><span class="sxs-lookup"><span data-stu-id="2c8fa-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="2c8fa-115">ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="2c8fa-116">gRPC-Web では、サービスを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="2c8fa-117">唯一の変更はスタートアップ構成です。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="2c8fa-118">ASP.NET Core gRPC サービスを使用して gRPC-Web を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="2c8fa-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="2c8fa-119">[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Web)パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="2c8fa-120">`AddGrpcWeb` と `UseGrpcWeb` を*Startup.cs*に追加して grpc-Web を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="2c8fa-121">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-121">The preceding code:</span></span>

* <span data-ttu-id="2c8fa-122">GRPC-Web ミドルウェア、`UseGrpcWeb`、およびエンドポイントの前に、を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="2c8fa-123">`endpoints.MapGrpcService<GreeterService>()` メソッドが `EnableGrpcWeb`で gRPC-Web をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="2c8fa-124">または、ConfigureServices に `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` を追加して、gRPC-Web をサポートするようにすべてのサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

<span data-ttu-id="2c8fa-125">CORS をサポートするように ASP.NET Core を構成するなど、ブラウザーから gRPC-Web を呼び出すには、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="2c8fa-126">詳細については、「 [CORS のサポート](xref:security/cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="2c8fa-127">ブラウザーから gRPC-Web を呼び出す</span><span class="sxs-lookup"><span data-stu-id="2c8fa-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="2c8fa-128">ブラウザーアプリは gRPC-Web を使用して gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="2c8fa-129">ブラウザーから gRPC-Web で gRPC サービスを呼び出すときには、いくつかの要件と制限があります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="2c8fa-130">サーバーは、gRPC-Web をサポートするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="2c8fa-131">クライアントストリーミングと双方向のストリーミング呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="2c8fa-132">サーバーストリーミングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-132">Server streaming is supported.</span></span>
* <span data-ttu-id="2c8fa-133">別のドメインで gRPC サービスを呼び出すには、サーバーで[CORS](xref:security/cors)を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="2c8fa-134">JavaScript gRPC-Web クライアント</span><span class="sxs-lookup"><span data-stu-id="2c8fa-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="2c8fa-135">JavaScript gRPC-Web クライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="2c8fa-136">JavaScript から gRPC-Web を使用する方法については、「 [grpc-web を使用して javascript クライアントコードを記述](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="2c8fa-137">.NET gRPC クライアントを使用して gRPC-Web を構成する</span><span class="sxs-lookup"><span data-stu-id="2c8fa-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="2c8fa-138">.NET gRPC クライアントは、gRPC-Web 呼び出しを行うように構成できます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="2c8fa-139">これは、ブラウザーでホストされ、JavaScript コードの HTTP 制限が同じである[Blazor WebAssembly](xref:blazor/index#blazor-webassembly)に便利です。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="2c8fa-140">.NET クライアントを使用した gRPC の呼び出しは[、HTTP/2 gRPC と同じ](xref:grpc/client)です。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="2c8fa-141">唯一の変更は、チャネルの作成方法です。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="2c8fa-142">GRPC-Web を使用するには:</span><span class="sxs-lookup"><span data-stu-id="2c8fa-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="2c8fa-143">[Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="2c8fa-144">[Grpc .Net クライアント](https://www.nuget.org/packages/Grpc.Net.Client)パッケージへの参照が2.27.0 以上であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-144">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="2c8fa-145">`GrpcWebHandler`を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-145">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="2c8fa-146">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-146">The preceding code:</span></span>

* <span data-ttu-id="2c8fa-147">GRPC-Web を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-147">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="2c8fa-148">クライアントを作成し、チャネルを使用して呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-148">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="2c8fa-149">`GrpcWebHandler` の作成時には、次の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-149">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="2c8fa-150">**Innerhandler**: GRPC HTTP 要求を行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler`など)。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-150">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="2c8fa-151">**Mode**: GRPC HTTP 要求要求 `Content-Type` が `application/grpc-web` または `application/grpc-web-text`かどうかを指定する列挙型。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-151">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="2c8fa-152">エンコードせずにコンテンツを送信するように構成 `GrpcWebMode.GrpcWeb` ます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-152">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="2c8fa-153">既定値。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-153">Default value.</span></span>
    * <span data-ttu-id="2c8fa-154">`GrpcWebMode.GrpcWebText` は、コンテンツを base64 でエンコードするように構成します。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-154">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="2c8fa-155">ブラウザーでのサーバーストリーム呼び出しに必要です。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-155">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="2c8fa-156">**HttpVersion**: http プロトコル `Version` 基になる grpc http 要求で[HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Version)を設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-156">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="2c8fa-157">gRPC-Web では特定のバージョンを必要とせず、指定しない限り、既定値は上書きされません。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-157">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2c8fa-158">生成された gRPC クライアントには、単項メソッドを呼び出すための同期メソッドと非同期メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-158">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="2c8fa-159">たとえば、`SayHello` は同期であり、`SayHelloAsync` は async です。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-159">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="2c8fa-160">Blazor WebAssembly で同期メソッドを呼び出すと、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-160">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="2c8fa-161">非同期メソッドは、常に Blazor WebAssembly 使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2c8fa-161">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c8fa-162">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2c8fa-162">Additional resources</span></span>

* [<span data-ttu-id="2c8fa-163">Web クライアント用 gRPC GitHub プロジェクト</span><span class="sxs-lookup"><span data-stu-id="2c8fa-163">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
