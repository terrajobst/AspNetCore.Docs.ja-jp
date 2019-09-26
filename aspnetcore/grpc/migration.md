---
title: GRPC サービスの C-core から ASP.NET Core への移行
author: juntaoluo
description: 既存の C コアベースの gRPC アプリを移動して ASP.NET Core スタック上で実行する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 8f0d9dd980fa3281f30dc29d329d10ccd352ae72
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278698"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="1bcfb-103">GRPC サービスの C-core から ASP.NET Core への移行</span><span class="sxs-lookup"><span data-stu-id="1bcfb-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="1bcfb-104">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="1bcfb-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="1bcfb-105">基になるスタックが実装されているため、 [C コアベースの gRPC](https://grpc.io/blog/grpc-stacks)アプリと ASP.NET Core ベースのアプリでは、すべての機能が同じ方法で動作するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="1bcfb-106">このドキュメントでは、2つのスタック間の移行に関する主な違いについて説明します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="1bcfb-107">gRPC サービスの実装の有効期間</span><span class="sxs-lookup"><span data-stu-id="1bcfb-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="1bcfb-108">ASP.NET Core スタックでは、既定で gRPC サービスが[スコープ有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1bcfb-109">これに対し、gRPC C-core は、既定では、[単一の有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を持つサービスにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="1bcfb-110">スコープ内の有効期間を使用すると、サービス実装は、スコープの有効期間を持つ他のサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="1bcfb-111">たとえば、スコープが指定された有効`DbContext`期間は、コンストラクターの挿入を通じて DI コンテナーから解決することもできます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="1bcfb-112">スコープ有効期間の使用:</span><span class="sxs-lookup"><span data-stu-id="1bcfb-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="1bcfb-113">サービス実装の新しいインスタンスは、要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="1bcfb-114">実装型のインスタンスメンバーを介して、要求間で状態を共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="1bcfb-115">共有状態は、単一のサービスに DI コンテナーに格納することを想定しています。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="1bcfb-116">格納されている共有状態は、gRPC サービス実装のコンストラクターで解決されます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="1bcfb-117">サービスの有効期間の詳細につい<xref:fundamentals/dependency-injection#service-lifetimes>ては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="1bcfb-118">シングルトンサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="1bcfb-118">Add a singleton service</span></span>

<span data-ttu-id="1bcfb-119">GRPC C コア実装から ASP.NET Core への移行を容易にするために、サービス実装のサービスの有効期間をスコープからシングルトンに変更することができます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="1bcfb-120">これには、サービス実装のインスタンスを DI コンテナーに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="1bcfb-121">ただし、シングルトンの有効期間を持つサービス実装では、コンストラクターの挿入によってスコープ付きサービスを解決できなくなりました。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="1bcfb-122">GRPC サービスのオプションの構成</span><span class="sxs-lookup"><span data-stu-id="1bcfb-122">Configure gRPC services options</span></span>

<span data-ttu-id="1bcfb-123">C コアベースのアプリでは`grpc.max_receive_message_length` 、や`grpc.max_send_message_length`などの設定は、[サーバーインスタンスを構築](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)するときにで`ChannelOption`構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="1bcfb-124">ASP.NET Core では、grpc は型を`GrpcServiceOptions`使用して構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="1bcfb-125">たとえば、gRPC サービスの最大受信メッセージサイズは、を使用`AddGrpc`して構成できます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="1bcfb-126">次の例では、 `ReceiveMaxMessageSize`既定の 4 mb を 16 mb に変更します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-126">The following example changes the default `ReceiveMaxMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="1bcfb-127">構成の詳細については<xref:grpc/configuration>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="1bcfb-128">ログの記録</span><span class="sxs-lookup"><span data-stu-id="1bcfb-128">Logging</span></span>

<span data-ttu-id="1bcfb-129">C コアベースのアプリは、を使用`GrpcEnvironment`して、デバッグの目的で[logger を構成](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="1bcfb-130">ASP.NET Core スタックは、[ログ API](xref:fundamentals/logging/index)を介してこの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1bcfb-131">たとえば、logger は、コンストラクターインジェクションを使用して gRPC サービスに追加できます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="1bcfb-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1bcfb-132">HTTPS</span></span>

<span data-ttu-id="1bcfb-133">C コアベースのアプリは、 [Server. Ports プロパティ](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)を使用して HTTPS を構成します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="1bcfb-134">同様の概念は、ASP.NET Core でサーバーを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="1bcfb-135">たとえば、Kestrel は、この機能に[エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="1bcfb-136">インターセプターとミドルウェア</span><span class="sxs-lookup"><span data-stu-id="1bcfb-136">Interceptors and Middleware</span></span>

<span data-ttu-id="1bcfb-137">ASP.NET Core[ミドルウェア](xref:fundamentals/middleware/index)は、C コアベースの grpc アプリのインターセプターと比較して同様の機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="1bcfb-138">ミドルウェアとインターセプターは、両方とも、gRPC 要求を処理するパイプラインを構築するために使用されるのと同じです。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-138">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="1bcfb-139">どちらも、パイプライン内の次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-139">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="1bcfb-140">ただし、ASP.NET Core ミドルウェアは基になる HTTP/2 メッセージに対して動作しますが、インターセプターは[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)を使用して、抽象の grpc レイヤーで動作します。</span><span class="sxs-lookup"><span data-stu-id="1bcfb-140">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bcfb-141">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1bcfb-141">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
