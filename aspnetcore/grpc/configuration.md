---
title: ASP.NET Core 構成用 gRPC
author: jamesnk
description: ASP.NET Core アプリ用に gRPC を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310313"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="bf926-103">ASP.NET Core 構成用 gRPC</span><span class="sxs-lookup"><span data-stu-id="bf926-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="bf926-104">サービスオプションの構成</span><span class="sxs-lookup"><span data-stu-id="bf926-104">Configure services options</span></span>

<span data-ttu-id="bf926-105">次の表では、gRPC サービスを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="bf926-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="bf926-106">オプション</span><span class="sxs-lookup"><span data-stu-id="bf926-106">Option</span></span> | <span data-ttu-id="bf926-107">既定値</span><span class="sxs-lookup"><span data-stu-id="bf926-107">Default Value</span></span> | <span data-ttu-id="bf926-108">説明</span><span class="sxs-lookup"><span data-stu-id="bf926-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="bf926-109">サーバーから送信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="bf926-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="bf926-110">構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="bf926-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="bf926-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="bf926-111">4 MB</span></span> | <span data-ttu-id="bf926-112">サーバーが受信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="bf926-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="bf926-113">サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="bf926-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="bf926-114">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf926-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="bf926-115">の`true`場合、サービスメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="bf926-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="bf926-116">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="bf926-116">The default is `false`.</span></span> <span data-ttu-id="bf926-117">を`EnableDetailedErrors`に`true`設定すると、機密情報が漏洩する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf926-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="bf926-118">gzip、deflate</span><span class="sxs-lookup"><span data-stu-id="bf926-118">gzip, deflate</span></span> | <span data-ttu-id="bf926-119">メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="bf926-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="bf926-120">カスタム圧縮プロバイダーを作成してコレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="bf926-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="bf926-121">既定で構成されているプロバイダーは、 **gzip**および**deflate**圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="bf926-121">The default configured providers support **gzip** and **deflate** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="bf926-122">サーバーから送信されたメッセージの圧縮に使用される圧縮アルゴリズム。</span><span class="sxs-lookup"><span data-stu-id="bf926-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="bf926-123">アルゴリズムは、の`CompressionProviders`圧縮プロバイダーと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf926-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="bf926-124">アルゴリズムで応答を圧縮するには、クライアントは、 **grpc-accept-encoding**ヘッダーでアルゴリズムを送信することによって、アルゴリズムをサポートしていることを示す必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf926-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="bf926-125">サーバーから送信されたメッセージの圧縮に使用される圧縮レベルです。</span><span class="sxs-lookup"><span data-stu-id="bf926-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="bf926-126">`AddGrpc` の`Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのサービスに対してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="bf926-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="bf926-127">1つのサービスのオプションは、で`AddGrpc`提供されるグローバルオプションを上書きします。また、次のものを使用し`AddServiceOptions<TService>`て構成できます。</span><span class="sxs-lookup"><span data-stu-id="bf926-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="bf926-128">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="bf926-128">Configure client options</span></span>

<span data-ttu-id="bf926-129">次の表では、gRPC チャネルを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="bf926-129">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="bf926-130">オプション</span><span class="sxs-lookup"><span data-stu-id="bf926-130">Option</span></span> | <span data-ttu-id="bf926-131">既定値</span><span class="sxs-lookup"><span data-stu-id="bf926-131">Default Value</span></span> | <span data-ttu-id="bf926-132">説明</span><span class="sxs-lookup"><span data-stu-id="bf926-132">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="bf926-133">新しいインスタンス</span><span class="sxs-lookup"><span data-stu-id="bf926-133">New instance</span></span> | <span data-ttu-id="bf926-134">Grpc 呼び出しを行うために使用する。`HttpClient`</span><span class="sxs-lookup"><span data-stu-id="bf926-134">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="bf926-135">クライアントを設定してカスタム`HttpClientHandler`を構成することも、grpc 呼び出し用に HTTP パイプラインにハンドラーを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="bf926-135">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="bf926-136">既定では、 `HttpClient`新しいインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bf926-136">By default a new `HttpClient` instance is created.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="bf926-137">クライアントから送信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="bf926-137">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="bf926-138">構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="bf926-138">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="bf926-139">4 MB</span><span class="sxs-lookup"><span data-stu-id="bf926-139">4 MB</span></span> | <span data-ttu-id="bf926-140">クライアントが受信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="bf926-140">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="bf926-141">サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="bf926-141">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="bf926-142">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf926-142">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `TransportOptions` | `null` | <span data-ttu-id="bf926-143">トランスポートオプションチャネルが gRPC サービスを呼び出す方法を構成します。</span><span class="sxs-lookup"><span data-stu-id="bf926-143">Transport options configure how the channel calls the gRPC service.</span></span> <span data-ttu-id="bf926-144">現在、唯一の実装`HttpClientTransport`オプションでは、 `HttpClient` grpc で使用するを指定できます。</span><span class="sxs-lookup"><span data-stu-id="bf926-144">Currently the only implementation is `HttpClientTransport` options lets you specify the `HttpClient` used by gRPC.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="bf926-145">`ChannelCredentials` のインスタンス。</span><span class="sxs-lookup"><span data-stu-id="bf926-145">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="bf926-146">資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="bf926-146">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="bf926-147">gzip、deflate</span><span class="sxs-lookup"><span data-stu-id="bf926-147">gzip, deflate</span></span> | <span data-ttu-id="bf926-148">メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="bf926-148">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="bf926-149">カスタム圧縮プロバイダーを作成してコレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="bf926-149">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="bf926-150">既定で構成されているプロバイダーは、 **gzip**および**deflate**圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="bf926-150">The default configured providers support **gzip** and **deflate** compression.</span></span> |

<span data-ttu-id="bf926-151">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="bf926-151">The following code:</span></span>

* <span data-ttu-id="bf926-152">チャネルの送信メッセージと受信メッセージの最大サイズを設定します。</span><span class="sxs-lookup"><span data-stu-id="bf926-152">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="bf926-153">クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="bf926-153">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="bf926-154">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bf926-154">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
