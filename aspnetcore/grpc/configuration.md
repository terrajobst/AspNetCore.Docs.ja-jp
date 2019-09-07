---
title: ASP.NET Core 構成用 gRPC
author: jamesnk
description: ASP.NET Core アプリ用に gRPC を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: d6f095820271a3bb07e05e29299fbb82b042983b
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773679"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="26db4-103">ASP.NET Core 構成用 gRPC</span><span class="sxs-lookup"><span data-stu-id="26db4-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="26db4-104">サービスオプションの構成</span><span class="sxs-lookup"><span data-stu-id="26db4-104">Configure services options</span></span>

<span data-ttu-id="26db4-105">次の表では、gRPC サービスを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="26db4-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="26db4-106">オプション</span><span class="sxs-lookup"><span data-stu-id="26db4-106">Option</span></span> | <span data-ttu-id="26db4-107">既定値</span><span class="sxs-lookup"><span data-stu-id="26db4-107">Default Value</span></span> | <span data-ttu-id="26db4-108">説明</span><span class="sxs-lookup"><span data-stu-id="26db4-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="26db4-109">サーバーから送信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="26db4-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="26db4-110">構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="26db4-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="26db4-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="26db4-111">4 MB</span></span> | <span data-ttu-id="26db4-112">サーバーが受信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="26db4-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="26db4-113">サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="26db4-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="26db4-114">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="26db4-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="26db4-115">の`true`場合、サービスメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="26db4-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="26db4-116">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="26db4-116">The default is `false`.</span></span> <span data-ttu-id="26db4-117">を`EnableDetailedErrors`に`true`設定すると、機密情報が漏洩する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="26db4-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="26db4-118">gzip</span><span class="sxs-lookup"><span data-stu-id="26db4-118">gzip</span></span> | <span data-ttu-id="26db4-119">メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="26db4-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="26db4-120">カスタム圧縮プロバイダーを作成してコレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="26db4-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="26db4-121">既定で構成されているプロバイダーは、 **gzip**圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="26db4-121">The default configured providers support **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="26db4-122">サーバーから送信されたメッセージの圧縮に使用される圧縮アルゴリズム。</span><span class="sxs-lookup"><span data-stu-id="26db4-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="26db4-123">アルゴリズムは、の`CompressionProviders`圧縮プロバイダーと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="26db4-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="26db4-124">アルゴリズムで応答を圧縮するには、クライアントは、 **grpc-accept-encoding**ヘッダーでアルゴリズムを送信することによって、アルゴリズムをサポートしていることを示す必要があります。</span><span class="sxs-lookup"><span data-stu-id="26db4-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="26db4-125">サーバーから送信されたメッセージの圧縮に使用される圧縮レベルです。</span><span class="sxs-lookup"><span data-stu-id="26db4-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="26db4-126">`AddGrpc` の`Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのサービスに対してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="26db4-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="26db4-127">1つのサービスのオプションは、で`AddGrpc`提供されるグローバルオプションを上書きします。また、次のものを使用し`AddServiceOptions<TService>`て構成できます。</span><span class="sxs-lookup"><span data-stu-id="26db4-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="26db4-128">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="26db4-128">Configure client options</span></span>

<span data-ttu-id="26db4-129">次の表では、gRPC チャネルを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="26db4-129">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="26db4-130">オプション</span><span class="sxs-lookup"><span data-stu-id="26db4-130">Option</span></span> | <span data-ttu-id="26db4-131">既定値</span><span class="sxs-lookup"><span data-stu-id="26db4-131">Default Value</span></span> | <span data-ttu-id="26db4-132">説明</span><span class="sxs-lookup"><span data-stu-id="26db4-132">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="26db4-133">新しいインスタンス</span><span class="sxs-lookup"><span data-stu-id="26db4-133">New instance</span></span> | <span data-ttu-id="26db4-134">Grpc 呼び出しを行うために使用する。`HttpClient`</span><span class="sxs-lookup"><span data-stu-id="26db4-134">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="26db4-135">クライアントを設定してカスタム`HttpClientHandler`を構成することも、grpc 呼び出し用に HTTP パイプラインにハンドラーを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="26db4-135">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="26db4-136">が指定されていない場合`HttpClient`は、チャネルに対して新しいインスタンスが作成されます。 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="26db4-136">If no `HttpClient` is specified, then a new `HttpClient` instance is created for the channel.</span></span> <span data-ttu-id="26db4-137">自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="26db4-137">It will automatically be disposed.</span></span> |
| `DisposeHttpClient` | `false` | <span data-ttu-id="26db4-138">`true` `GrpcChannel` と`HttpClient`が指定されている場合は、が破棄されると、インスタンスが破棄されます。 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="26db4-138">If `true`, and an `HttpClient` is specified, then the `HttpClient` instance will be disposed when the `GrpcChannel` is disposed.</span></span> |
| `LoggerFactory` | `null` | <span data-ttu-id="26db4-139">Grpc 呼び出しに関する情報をログに記録するためにクライアントによって使用される。`LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="26db4-139">The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="26db4-140">インスタンス`LoggerFactory`は、依存関係の挿入から解決すること`LoggerFactory.Create`も、を使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="26db4-140">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="26db4-141">ログ記録を構成する例に<xref:fundamentals/logging/index>ついては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="26db4-141">For examples of configuring logging, see <xref:fundamentals/logging/index>.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="26db4-142">クライアントから送信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="26db4-142">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="26db4-143">構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="26db4-143">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="26db4-144">4 MB</span><span class="sxs-lookup"><span data-stu-id="26db4-144">4 MB</span></span> | <span data-ttu-id="26db4-145">クライアントが受信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="26db4-145">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="26db4-146">クライアントがこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="26db4-146">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="26db4-147">この値を大きくすると、クライアントはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="26db4-147">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="26db4-148">`ChannelCredentials` のインスタンス。</span><span class="sxs-lookup"><span data-stu-id="26db4-148">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="26db4-149">資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="26db4-149">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="26db4-150">gzip</span><span class="sxs-lookup"><span data-stu-id="26db4-150">gzip</span></span> | <span data-ttu-id="26db4-151">メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="26db4-151">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="26db4-152">カスタム圧縮プロバイダーを作成してコレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="26db4-152">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="26db4-153">既定で構成されているプロバイダーは、 **gzip**圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="26db4-153">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="26db4-154">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="26db4-154">The following code:</span></span>

* <span data-ttu-id="26db4-155">チャネルの送信メッセージと受信メッセージの最大サイズを設定します。</span><span class="sxs-lookup"><span data-stu-id="26db4-155">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="26db4-156">クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="26db4-156">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="26db4-157">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="26db4-157">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
