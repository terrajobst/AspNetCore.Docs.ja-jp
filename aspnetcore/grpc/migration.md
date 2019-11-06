---
title: GRPC サービスの C-core から ASP.NET Core への移行
author: juntaoluo
description: 既存の C コアベースの gRPC アプリを移動して ASP.NET Core スタック上で実行する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: c4c07808540c9af370bfa253e8154a8a19f0f3de
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634063"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="3102e-103">GRPC サービスの C-core から ASP.NET Core への移行</span><span class="sxs-lookup"><span data-stu-id="3102e-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="3102e-104">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="3102e-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="3102e-105">基になるスタックが実装されているため、 [C コアベースの gRPC](https://grpc.io/blog/grpc-stacks)アプリと ASP.NET Core ベースのアプリでは、すべての機能が同じ方法で動作するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="3102e-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="3102e-106">このドキュメントでは、2つのスタック間の移行に関する主な違いについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3102e-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="3102e-107">gRPC サービスの実装の有効期間</span><span class="sxs-lookup"><span data-stu-id="3102e-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="3102e-108">ASP.NET Core スタックでは、既定で gRPC サービスが[スコープ有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で作成されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3102e-109">これに対し、gRPC C-core は、既定では、[単一の有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を持つサービスにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="3102e-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="3102e-110">スコープ内の有効期間を使用すると、サービス実装は、スコープの有効期間を持つ他のサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="3102e-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="3102e-111">たとえば、スコープが指定された有効期間は、コンストラクターの挿入によって DI コンテナーから `DbContext` を解決することもできます。</span><span class="sxs-lookup"><span data-stu-id="3102e-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="3102e-112">スコープ有効期間の使用:</span><span class="sxs-lookup"><span data-stu-id="3102e-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="3102e-113">サービス実装の新しいインスタンスは、要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="3102e-114">実装型のインスタンスメンバーを介して、要求間で状態を共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="3102e-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="3102e-115">共有状態は、単一のサービスに DI コンテナーに格納することを想定しています。</span><span class="sxs-lookup"><span data-stu-id="3102e-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="3102e-116">格納されている共有状態は、gRPC サービス実装のコンストラクターで解決されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="3102e-117">サービスの有効期間の詳細については、「<xref:fundamentals/dependency-injection#service-lifetimes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3102e-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="3102e-118">シングルトンサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="3102e-118">Add a singleton service</span></span>

<span data-ttu-id="3102e-119">GRPC C コア実装から ASP.NET Core への移行を容易にするために、サービス実装のサービスの有効期間をスコープからシングルトンに変更することができます。</span><span class="sxs-lookup"><span data-stu-id="3102e-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="3102e-120">これには、サービス実装のインスタンスを DI コンテナーに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3102e-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="3102e-121">ただし、シングルトンの有効期間を持つサービス実装では、コンストラクターの挿入によってスコープ付きサービスを解決できなくなりました。</span><span class="sxs-lookup"><span data-stu-id="3102e-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="3102e-122">GRPC サービスのオプションの構成</span><span class="sxs-lookup"><span data-stu-id="3102e-122">Configure gRPC services options</span></span>

<span data-ttu-id="3102e-123">C コアベースのアプリでは、`grpc.max_receive_message_length` や `grpc.max_send_message_length` などの設定は、[サーバーインスタンスの構築](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)時に `ChannelOption` で構成されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="3102e-124">ASP.NET Core では、gRPC は `GrpcServiceOptions` の種類を使用して構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="3102e-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="3102e-125">たとえば、gRPC サービスの最大受信メッセージサイズは、`AddGrpc` を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="3102e-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="3102e-126">次の例では、既定の `MaxReceiveMessageSize` を 4 MB から 16 MB に変更します。</span><span class="sxs-lookup"><span data-stu-id="3102e-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="3102e-127">構成の詳細については、「<xref:grpc/configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3102e-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="3102e-128">ログの記録</span><span class="sxs-lookup"><span data-stu-id="3102e-128">Logging</span></span>

<span data-ttu-id="3102e-129">C コアベースのアプリは、デバッグの目的で[logger を構成](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)するために `GrpcEnvironment` に依存しています。</span><span class="sxs-lookup"><span data-stu-id="3102e-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="3102e-130">ASP.NET Core スタックは、[ログ API](xref:fundamentals/logging/index)を介してこの機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="3102e-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3102e-131">たとえば、logger は、コンストラクターインジェクションを使用して gRPC サービスに追加できます。</span><span class="sxs-lookup"><span data-stu-id="3102e-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="3102e-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3102e-132">HTTPS</span></span>

<span data-ttu-id="3102e-133">C コアベースのアプリは、 [Server. Ports プロパティ](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)を使用して HTTPS を構成します。</span><span class="sxs-lookup"><span data-stu-id="3102e-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="3102e-134">同様の概念は、ASP.NET Core でサーバーを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="3102e-135">たとえば、Kestrel は、この機能に[エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)を使用します。</span><span class="sxs-lookup"><span data-stu-id="3102e-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="3102e-136">gRPC インターセプターとミドルウェア</span><span class="sxs-lookup"><span data-stu-id="3102e-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="3102e-137">ASP.NET Core[ミドルウェア](xref:fundamentals/middleware/index)は、C コアベースの grpc アプリのインターセプターと比較して同様の機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="3102e-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="3102e-138">ASP.NET Core ミドルウェアとインターセプターは概念的に似ています。</span><span class="sxs-lookup"><span data-stu-id="3102e-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="3102e-139">両方とも：</span><span class="sxs-lookup"><span data-stu-id="3102e-139">Both:</span></span>

* <span data-ttu-id="3102e-140">は、gRPC 要求を処理するパイプラインを構築するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="3102e-141">パイプライン内の次のコンポーネントの前または後に作業を実行することを許可します。</span><span class="sxs-lookup"><span data-stu-id="3102e-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="3102e-142">`HttpContext`へのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3102e-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="3102e-143">ミドルウェアでは、`HttpContext` はパラメーターです。</span><span class="sxs-lookup"><span data-stu-id="3102e-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="3102e-144">インターセプターでは、`ServerCallContext.GetHttpContext` 拡張メソッドで `ServerCallContext` パラメーターを使用して `HttpContext` にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3102e-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="3102e-145">この機能は、ASP.NET Core で実行されているインターセプターに固有の機能であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3102e-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="3102e-146">gRPC インターセプターと ASP.NET Core ミドルウェアの違い:</span><span class="sxs-lookup"><span data-stu-id="3102e-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="3102e-147">インターセプター</span><span class="sxs-lookup"><span data-stu-id="3102e-147">Interceptors:</span></span>
  * <span data-ttu-id="3102e-148">[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)を使用して、抽象化の grpc レイヤーを操作します。</span><span class="sxs-lookup"><span data-stu-id="3102e-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="3102e-149">次へのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3102e-149">Provide access to:</span></span>
    * <span data-ttu-id="3102e-150">呼び出しに送信された逆シリアル化されたメッセージ。</span><span class="sxs-lookup"><span data-stu-id="3102e-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="3102e-151">シリアル化される前に、呼び出しから返されるメッセージ。</span><span class="sxs-lookup"><span data-stu-id="3102e-151">The message being returned from the call before it is serialized.</span></span>
* <span data-ttu-id="3102e-152">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="3102e-152">Middleware:</span></span>
  * <span data-ttu-id="3102e-153">GRPC インターセプターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="3102e-153">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="3102e-154">は、基になる HTTP/2 メッセージを操作します。</span><span class="sxs-lookup"><span data-stu-id="3102e-154">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="3102e-155">は、要求ストリームと応答ストリームからのみバイトにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3102e-155">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3102e-156">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3102e-156">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
