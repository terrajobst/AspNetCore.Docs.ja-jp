---
title: GRPC での ASP.NET Core のセキュリティに関する考慮事項
author: jamesnk
description: GRPC for ASP.NET Core のセキュリティに関する考慮事項について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: 4a70cb16d8397dbc69a626435fb72a0512788b14
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308755"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="33d80-103">GRPC での ASP.NET Core のセキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="33d80-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="33d80-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="33d80-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="33d80-105">この記事では、.NET Core を使用して gRPC をセキュリティで保護する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="33d80-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="33d80-106">トランスポートセキュリティ</span><span class="sxs-lookup"><span data-stu-id="33d80-106">Transport security</span></span>

<span data-ttu-id="33d80-107">gRPC メッセージは、HTTP/2 を使用して送受信されます。</span><span class="sxs-lookup"><span data-stu-id="33d80-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="33d80-108">次のことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="33d80-108">We recommend:</span></span>

* <span data-ttu-id="33d80-109">[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用して、運用環境の grpc アプリでメッセージをセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="33d80-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="33d80-110">gRPC サービスは、セキュリティで保護されたポートをリッスンし、応答するだけです。</span><span class="sxs-lookup"><span data-stu-id="33d80-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="33d80-111">TLS は Kestrel で構成されます。</span><span class="sxs-lookup"><span data-stu-id="33d80-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="33d80-112">Kestrel エンドポイントの構成の詳細については、「 [kestrel エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="33d80-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="33d80-113">例外</span><span class="sxs-lookup"><span data-stu-id="33d80-113">Exceptions</span></span>

<span data-ttu-id="33d80-114">例外メッセージは、通常、クライアントに公開されない機密データと見なされます。</span><span class="sxs-lookup"><span data-stu-id="33d80-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="33d80-115">既定では、gRPC は gRPC サービスによってスローされた例外の詳細をクライアントに送信しません。</span><span class="sxs-lookup"><span data-stu-id="33d80-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="33d80-116">代わりに、エラーが発生したことを示す一般的なメッセージをクライアントが受信します。</span><span class="sxs-lookup"><span data-stu-id="33d80-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="33d80-117">クライアントへの例外メッセージの配信は、 [EnableDetailedErrors](xref:grpc/configuration#configure-services-options)を使用して (開発やテストなどで) オーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="33d80-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="33d80-118">例外メッセージを実稼働アプリでクライアントに公開することはできません。</span><span class="sxs-lookup"><span data-stu-id="33d80-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="33d80-119">メッセージサイズの制限</span><span class="sxs-lookup"><span data-stu-id="33d80-119">Message size limits</span></span>

<span data-ttu-id="33d80-120">GRPC クライアントおよびサービスへの着信メッセージは、メモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="33d80-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="33d80-121">メッセージサイズの制限は、gRPC が過剰なリソースを消費しないようにするためのメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="33d80-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="33d80-122">gRPC では、メッセージごとのサイズ制限を使用して、受信メッセージと送信メッセージを管理します。</span><span class="sxs-lookup"><span data-stu-id="33d80-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="33d80-123">既定では、gRPC は受信メッセージを 4 MB に制限します。</span><span class="sxs-lookup"><span data-stu-id="33d80-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="33d80-124">送信メッセージに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="33d80-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="33d80-125">サーバーでは、次のように`AddGrpc`して、アプリケーションのすべてのサービスに対して grpc メッセージ制限を構成できます。</span><span class="sxs-lookup"><span data-stu-id="33d80-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 1 * 1024 * 1024;  // 1 megabyte
        options.SendMaxMessageSize = 1 * 1024 * 1024;     // 1 megabyte
    });
}
```

<span data-ttu-id="33d80-126">また、を使用し`AddServiceOptions<TService>`て、個々のサービスに対して制限を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="33d80-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="33d80-127">メッセージサイズの制限の構成の詳細については、「 [Grpc の構成](xref:grpc/configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="33d80-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="33d80-128">クライアント証明書の検証</span><span class="sxs-lookup"><span data-stu-id="33d80-128">Client certificate validation</span></span>

<span data-ttu-id="33d80-129">[クライアント証明書](https://tools.ietf.org/html/rfc5246#section-7.4.4)は、接続が確立されると最初に検証されます。</span><span class="sxs-lookup"><span data-stu-id="33d80-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="33d80-130">既定では、Kestrel は接続のクライアント証明書の追加の検証を実行しません。</span><span class="sxs-lookup"><span data-stu-id="33d80-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="33d80-131">クライアント証明書によって保護されている gRPC サービスでは、 [AspNetCore](xref:security/authentication/certauth)パッケージを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="33d80-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="33d80-132">証明書認証 ASP.NET Core は、次のようなクライアント証明書に対して追加の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="33d80-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="33d80-133">証明書に有効な拡張キー使用法 (EKU) がある</span><span class="sxs-lookup"><span data-stu-id="33d80-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="33d80-134">有効期間内</span><span class="sxs-lookup"><span data-stu-id="33d80-134">Is within its validity period</span></span>
* <span data-ttu-id="33d80-135">証明書の失効を確認する</span><span class="sxs-lookup"><span data-stu-id="33d80-135">Check certificate revocation</span></span>
