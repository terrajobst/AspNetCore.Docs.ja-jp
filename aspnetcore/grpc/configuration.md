---
title: gRPC for .NET 構成
author: jamesnk
description: .NET アプリ用に gRPC を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: 42574b43b4751efc37ff3a827716df4cb8130842
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71199086"
---
# <a name="grpc-for-net-configuration"></a><span data-ttu-id="4bb85-103">gRPC for .NET 構成</span><span class="sxs-lookup"><span data-stu-id="4bb85-103">gRPC for .NET configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="4bb85-104">サービスオプションの構成</span><span class="sxs-lookup"><span data-stu-id="4bb85-104">Configure services options</span></span>

<span data-ttu-id="4bb85-105">grpc サービスは`AddGrpc` *Startup.cs*ので構成されます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-105">gRPC services are configured with `AddGrpc` in *Startup.cs*.</span></span> <span data-ttu-id="4bb85-106">次の表では、gRPC サービスを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-106">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="4bb85-107">オプション</span><span class="sxs-lookup"><span data-stu-id="4bb85-107">Option</span></span> | <span data-ttu-id="4bb85-108">既定値</span><span class="sxs-lookup"><span data-stu-id="4bb85-108">Default Value</span></span> | <span data-ttu-id="4bb85-109">説明</span><span class="sxs-lookup"><span data-stu-id="4bb85-109">Description</span></span> |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="4bb85-110">サーバーから送信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="4bb85-110">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="4bb85-111">構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-111">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="4bb85-112">4 MB</span><span class="sxs-lookup"><span data-stu-id="4bb85-112">4 MB</span></span> | <span data-ttu-id="4bb85-113">サーバーが受信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="4bb85-113">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="4bb85-114">サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-114">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="4bb85-115">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4bb85-115">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4bb85-116">の`true`場合、サービスメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-116">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="4bb85-117">既定値は、`false` です。</span><span class="sxs-lookup"><span data-stu-id="4bb85-117">The default is `false`.</span></span> <span data-ttu-id="4bb85-118">を`EnableDetailedErrors`に`true`設定すると、機密情報が漏洩する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4bb85-118">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="4bb85-119">gzip</span><span class="sxs-lookup"><span data-stu-id="4bb85-119">gzip</span></span> | <span data-ttu-id="4bb85-120">メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="4bb85-120">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="4bb85-121">カスタム圧縮プロバイダーを作成してコレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-121">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="4bb85-122">既定で構成されているプロバイダーは、 **gzip**圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4bb85-122">The default configured providers support **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="4bb85-123">サーバーから送信されたメッセージの圧縮に使用される圧縮アルゴリズム。</span><span class="sxs-lookup"><span data-stu-id="4bb85-123">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="4bb85-124">アルゴリズムは、の`CompressionProviders`圧縮プロバイダーと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="4bb85-124">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="4bb85-125">アルゴリズムで応答を圧縮するには、クライアントは、 **grpc-accept-encoding**ヘッダーでアルゴリズムを送信することによって、アルゴリズムをサポートしていることを示す必要があります。</span><span class="sxs-lookup"><span data-stu-id="4bb85-125">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="4bb85-126">サーバーから送信されたメッセージの圧縮に使用される圧縮レベルです。</span><span class="sxs-lookup"><span data-stu-id="4bb85-126">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="4bb85-127">`AddGrpc` の`Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのサービスに対してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-127">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="4bb85-128">1つのサービスのオプションは、で`AddGrpc`提供されるグローバルオプションを上書きします。また、次のものを使用し`AddServiceOptions<TService>`て構成できます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-128">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="4bb85-129">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="4bb85-129">Configure client options</span></span>

<span data-ttu-id="4bb85-130">gRPC クライアントの構成がオン`GrpcChannelOptions`になっています。</span><span class="sxs-lookup"><span data-stu-id="4bb85-130">gRPC client configuration is set on `GrpcChannelOptions`.</span></span> <span data-ttu-id="4bb85-131">次の表では、gRPC チャネルを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-131">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="4bb85-132">オプション</span><span class="sxs-lookup"><span data-stu-id="4bb85-132">Option</span></span> | <span data-ttu-id="4bb85-133">既定値</span><span class="sxs-lookup"><span data-stu-id="4bb85-133">Default Value</span></span> | <span data-ttu-id="4bb85-134">説明</span><span class="sxs-lookup"><span data-stu-id="4bb85-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `HttpClient` | <span data-ttu-id="4bb85-135">新しいインスタンス</span><span class="sxs-lookup"><span data-stu-id="4bb85-135">New instance</span></span> | <span data-ttu-id="4bb85-136">Grpc 呼び出しを行うために使用する。`HttpClient`</span><span class="sxs-lookup"><span data-stu-id="4bb85-136">The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="4bb85-137">クライアントを設定してカスタム`HttpClientHandler`を構成することも、grpc 呼び出し用に HTTP パイプラインにハンドラーを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-137">A client can be set to configure a custom `HttpClientHandler`, or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="4bb85-138">が指定されていない場合`HttpClient`は、チャネルに対して新しいインスタンスが作成されます。 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="4bb85-138">If no `HttpClient` is specified, then a new `HttpClient` instance is created for the channel.</span></span> <span data-ttu-id="4bb85-139">自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-139">It will automatically be disposed.</span></span> |
| `DisposeHttpClient` | `false` | <span data-ttu-id="4bb85-140">`true` `GrpcChannel` と`HttpClient`が指定されている場合は、が破棄されると、インスタンスが破棄されます。 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="4bb85-140">If `true`, and an `HttpClient` is specified, then the `HttpClient` instance will be disposed when the `GrpcChannel` is disposed.</span></span> |
| `LoggerFactory` | `null` | <span data-ttu-id="4bb85-141">Grpc 呼び出しに関する情報をログに記録するためにクライアントによって使用される。`LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="4bb85-141">The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="4bb85-142">インスタンス`LoggerFactory`は、依存関係の挿入から解決すること`LoggerFactory.Create`も、を使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-142">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="4bb85-143">ログ記録を構成する例に<xref:grpc/diagnostics#grpc-client-logging>ついては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4bb85-143">For examples of configuring logging, see <xref:grpc/diagnostics#grpc-client-logging>.</span></span> |
| `MaxSendMessageSize` | `null` | <span data-ttu-id="4bb85-144">クライアントから送信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="4bb85-144">The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="4bb85-145">構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-145">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `MaxReceiveMessageSize` | <span data-ttu-id="4bb85-146">4 MB</span><span class="sxs-lookup"><span data-stu-id="4bb85-146">4 MB</span></span> | <span data-ttu-id="4bb85-147">クライアントが受信できる最大メッセージサイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="4bb85-147">The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="4bb85-148">クライアントがこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-148">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="4bb85-149">この値を大きくすると、クライアントはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4bb85-149">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `Credentials` | `null` | <span data-ttu-id="4bb85-150">`ChannelCredentials` のインスタンス。</span><span class="sxs-lookup"><span data-stu-id="4bb85-150">A `ChannelCredentials` instance.</span></span> <span data-ttu-id="4bb85-151">資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-151">Credentials are used to add authentication metadata to gRPC calls.</span></span> |
| `CompressionProviders` | <span data-ttu-id="4bb85-152">gzip</span><span class="sxs-lookup"><span data-stu-id="4bb85-152">gzip</span></span> | <span data-ttu-id="4bb85-153">メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="4bb85-153">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="4bb85-154">カスタム圧縮プロバイダーを作成してコレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="4bb85-154">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="4bb85-155">既定で構成されているプロバイダーは、 **gzip**圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4bb85-155">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="4bb85-156">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-156">The following code:</span></span>

* <span data-ttu-id="4bb85-157">チャネルの送信メッセージと受信メッセージの最大サイズを設定します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-157">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="4bb85-158">クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="4bb85-158">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a><span data-ttu-id="4bb85-159">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4bb85-159">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
