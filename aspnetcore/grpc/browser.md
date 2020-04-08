---
title: ブラウザー アプリでの gRPC の使用
author: jamesnk
description: ASP.NET Core で、GRPC-Web を使用してブラウザー アプリから呼び出しできるように、gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649418"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="73c0c-103">ブラウザー アプリでの gRPC の使用</span><span class="sxs-lookup"><span data-stu-id="73c0c-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="73c0c-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="73c0c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73c0c-105">**.NET での gRPC-Web のサポートは試験段階です**</span><span class="sxs-lookup"><span data-stu-id="73c0c-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="73c0c-106">gRPC-Web for .NET は、コミットされた製品ではなく、試験段階のプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="73c0c-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="73c0c-107">次の目的があります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-107">We want to:</span></span>
>
> * <span data-ttu-id="73c0c-108">gRPC-Web の実装方法が機能することをテストします。</span><span class="sxs-lookup"><span data-stu-id="73c0c-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="73c0c-109">.NET 開発者にとって、プロキシ経由で gRPC-Web を設定する従来の方法と比較して、この方法が有益であるかどうかについてのフィードバックを得ます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="73c0c-110">開発者が望み、生産性が向上するものを構築するために、[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) でフィードバックをお寄せください。</span><span class="sxs-lookup"><span data-stu-id="73c0c-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="73c0c-111">ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="73c0c-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="73c0c-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) は、ブラウザーの JavaScript および Blazor アプリで gRPC サービスを呼び出せるようにするプロトコルです。</span><span class="sxs-lookup"><span data-stu-id="73c0c-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="73c0c-113">この記事では、.NET Core で gRPC-Web を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="73c0c-114">ASP.NET Core での gRPC-Web の構成</span><span class="sxs-lookup"><span data-stu-id="73c0c-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="73c0c-115">ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="73c0c-116">gRPC-Web では、サービスを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="73c0c-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="73c0c-117">唯一の変更点はスタートアップ構成です。</span><span class="sxs-lookup"><span data-stu-id="73c0c-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="73c0c-118">ASP.NET Core gRPC サービスで gRPC-Web を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="73c0c-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="73c0c-119">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="73c0c-120">アプリで gRPC-Web を使用するように構成するには、`AddGrpcWeb` と `UseGrpcWeb` を *Startup.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="73c0c-121">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-121">The preceding code:</span></span>

* <span data-ttu-id="73c0c-122">GRPC-Web ミドルウェアの `UseGrpcWeb` を、ルーティングの後かつエンドポイントの前に追加します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="73c0c-123">`endpoints.MapGrpcService<GreeterService>()` メソッドで、`EnableGrpcWeb` を使用して gRPC-Web をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="73c0c-124">または、ConfigureServices に `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` を追加して、すべてのサービスで gRPC-Web をサポートするように構成します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a><span data-ttu-id="73c0c-125">gRPC-Web と CORS</span><span class="sxs-lookup"><span data-stu-id="73c0c-125">gRPC-Web and CORS</span></span>

<span data-ttu-id="73c0c-126">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="73c0c-126">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="73c0c-127">この制限は、ブラウザー アプリで gRPC-Web 呼び出しを行う場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-127">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="73c0c-128">たとえば、`https://www.contoso.com` によって提供されるブラウザー アプリでは、`https://services.contoso.com` でホストされている gRPC-Web サービスの呼び出しがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-128">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="73c0c-129">この制限を緩和するには、クロス オリジン リソース共有 (CORS) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-129">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="73c0c-130">ブラウザー アプリでクロス オリジン gRPC-Web 呼び出しを行えるようにするには、[ASP.NET Core で CORS](xref:security/cors) を設定します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-130">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="73c0c-131">組み込みの CORS サポートを使用し、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> で gRPC 固有のヘッダーを公開します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-131">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="73c0c-132">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-132">The preceding code:</span></span>

* <span data-ttu-id="73c0c-133">`AddCors` を呼び出して CORS サービスを追加し、gRPC 固有のヘッダーを公開する CORS ポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-133">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="73c0c-134">`UseCors` を呼び出して、ルーティングの後かつエンドポイントの前に CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-134">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="73c0c-135">`endpoints.MapGrpcService<GreeterService>()` メソッドが `RequiresCors` で CORS をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-135">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="73c0c-136">ブラウザーから gRPC-Web を呼び出す</span><span class="sxs-lookup"><span data-stu-id="73c0c-136">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="73c0c-137">ブラウザー アプリでは gRPC-Web を使用して gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-137">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="73c0c-138">ブラウザーから gRPC-Web を使用して gRPC サービスを呼び出す場合、いくつかの要件と制限があります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-138">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="73c0c-139">サーバーは、gRPC-Web をサポートするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-139">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="73c0c-140">クライアント ストリーミングと双方向ストリーミングの呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="73c0c-140">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="73c0c-141">サーバー ストリーミングはサポートされています。</span><span class="sxs-lookup"><span data-stu-id="73c0c-141">Server streaming is supported.</span></span>
* <span data-ttu-id="73c0c-142">別のドメインで gRPC サービスを呼び出すには、サーバーで [CORS](xref:security/cors) を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-142">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="73c0c-143">JavaScript gRPC-Web クライアント</span><span class="sxs-lookup"><span data-stu-id="73c0c-143">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="73c0c-144">JavaScript gRPC-Web クライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-144">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="73c0c-145">JavaScript から gRPC-Web を使用する方法については、[gRPC-Web を使用した JavaScript クライアント コードの作成](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="73c0c-145">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="73c0c-146">.NET gRPC クライアントを使用して gRPC-Web を構成する</span><span class="sxs-lookup"><span data-stu-id="73c0c-146">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="73c0c-147">gRPC-Web 呼び出しを行うように .NET gRPC クライアントを構成できます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-147">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="73c0c-148">これは、ブラウザーでホストされ、JavaScript コードの同じ HTTP 制限がある [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) アプリに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-148">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="73c0c-149">.NET クライアントによる gRPC-Web の呼び出しは、[HTTP/2 gRPC と同じ](xref:grpc/client)です。</span><span class="sxs-lookup"><span data-stu-id="73c0c-149">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="73c0c-150">唯一の変更点は、チャネルの作成方法です。</span><span class="sxs-lookup"><span data-stu-id="73c0c-150">The only modification is how the channel is created.</span></span>

<span data-ttu-id="73c0c-151">gRPC-Web を使用するには:</span><span class="sxs-lookup"><span data-stu-id="73c0c-151">To use gRPC-Web:</span></span>

* <span data-ttu-id="73c0c-152">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-152">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="73c0c-153">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) パッケージへの参照が 2.27.0 以上であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-153">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="73c0c-154">`GrpcWebHandler` を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-154">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="73c0c-155">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="73c0c-155">The preceding code:</span></span>

* <span data-ttu-id="73c0c-156">gRPC-Web を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-156">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="73c0c-157">クライアントを作成し、チャネルを使用して呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="73c0c-157">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="73c0c-158">作成時に、`GrpcWebHandler` には次の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-158">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="73c0c-159">**InnerHandler**:gRPC HTTP 要求を行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler` など)。</span><span class="sxs-lookup"><span data-stu-id="73c0c-159">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="73c0c-160">**[モード]** :gRPC HTTP 要求の `Content-Type` が `application/grpc-web` または `application/grpc-web-text` であるかどうかを指定する列挙型。</span><span class="sxs-lookup"><span data-stu-id="73c0c-160">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="73c0c-161">`GrpcWebMode.GrpcWeb` は、コンテンツをエンコードせずに送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-161">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="73c0c-162">既定値です。</span><span class="sxs-lookup"><span data-stu-id="73c0c-162">Default value.</span></span>
    * <span data-ttu-id="73c0c-163">`GrpcWebMode.GrpcWebText` は、コンテンツを base64 でエンコードするように構成します。</span><span class="sxs-lookup"><span data-stu-id="73c0c-163">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="73c0c-164">ブラウザーでのサーバー ストリーム呼び出しに必要です。</span><span class="sxs-lookup"><span data-stu-id="73c0c-164">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="73c0c-165">**HttpVersion**:基になる gRPC HTTP 要求で、[HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Version) を設定するために使用される HTTP プロトコル `Version`。</span><span class="sxs-lookup"><span data-stu-id="73c0c-165">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="73c0c-166">gRPC-Web では特定のバージョンを必要とせず、指定しない限り、既定値はオーバーライドされません。</span><span class="sxs-lookup"><span data-stu-id="73c0c-166">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73c0c-167">生成された gRPC クライアントには、単項メソッドを呼び出すための同期メソッドと非同期メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-167">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="73c0c-168">たとえば、`SayHello` は同期であり、`SayHelloAsync` は非同期です。</span><span class="sxs-lookup"><span data-stu-id="73c0c-168">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="73c0c-169">Blazor WebAssembly アプリで同期メソッドを呼び出すと、アプリが応答しなくなります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-169">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="73c0c-170">Blazor WebAssembly では、常に非同期メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="73c0c-170">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73c0c-171">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="73c0c-171">Additional resources</span></span>

* [<span data-ttu-id="73c0c-172">Web クライアント用 gRPC の GitHub プロジェクト</span><span class="sxs-lookup"><span data-stu-id="73c0c-172">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
