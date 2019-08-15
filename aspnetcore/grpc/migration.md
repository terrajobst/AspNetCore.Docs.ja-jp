---
title: GRPC サービスの C-core から ASP.NET Core への移行
author: juntaoluo
description: 既存の C コアベースの gRPC アプリを移動して ASP.NET Core スタック上で実行する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 39aa711a1a47cf11ec5b08903b4130c7caa1501c
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022296"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>GRPC サービスの C-core から ASP.NET Core への移行

作成者: [John Luo](https://github.com/juntaoluo)

基になるスタックが実装されているため、 [C コアベースの gRPC](https://grpc.io/blog/grpc-stacks)アプリと ASP.NET Core ベースのアプリでは、すべての機能が同じ方法で動作するわけではありません。 このドキュメントでは、2つのスタック間の移行に関する主な違いについて説明します。

## <a name="grpc-service-implementation-lifetime"></a>gRPC サービスの実装の有効期間

ASP.NET Core スタックでは、既定で gRPC サービスが[スコープ有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で作成されます。 これに対し、gRPC C-core は、既定では、[単一の有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を持つサービスにバインドされます。

スコープ内の有効期間を使用すると、サービス実装は、スコープの有効期間を持つ他のサービスを解決できます。 たとえば、スコープが指定された有効`DbContext`期間は、コンストラクターの挿入を通じて DI コンテナーから解決することもできます。 スコープ有効期間の使用:

* サービス実装の新しいインスタンスは、要求ごとに作成されます。
* 実装型のインスタンスメンバーを介して、要求間で状態を共有することはできません。
* 共有状態は、単一のサービスに DI コンテナーに格納することを想定しています。 格納されている共有状態は、gRPC サービス実装のコンストラクターで解決されます。

サービスの有効期間の詳細につい<xref:fundamentals/dependency-injection#service-lifetimes>ては、「」を参照してください。

### <a name="add-a-singleton-service"></a>シングルトンサービスを追加する

GRPC C コア実装から ASP.NET Core への移行を容易にするために、サービス実装のサービスの有効期間をスコープからシングルトンに変更することができます。 これには、サービス実装のインスタンスを DI コンテナーに追加する必要があります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

ただし、シングルトンの有効期間を持つサービス実装では、コンストラクターの挿入によってスコープ付きサービスを解決できなくなりました。

## <a name="configure-grpc-services-options"></a>GRPC サービスのオプションの構成

C コアベースのアプリでは`grpc.max_receive_message_length` 、や`grpc.max_send_message_length`などの設定は、[サーバーインスタンスを構築](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)するときにで`ChannelOption`構成されます。

ASP.NET Core では、grpc は型を`GrpcServiceOptions`使用して構成を提供します。 たとえば、gRPC サービスの最大受信メッセージサイズは、 `AddGrpc`次の方法で構成できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

構成の詳細については<xref:grpc/configuration>、「」を参照してください。

## <a name="logging"></a>ログの記録

C コアベースのアプリは、を使用`GrpcEnvironment`して、デバッグの目的で[logger を構成](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)します。 ASP.NET Core スタックは、[ログ API](xref:fundamentals/logging/index)を介してこの機能を提供します。 たとえば、logger は、コンストラクターインジェクションを使用して gRPC サービスに追加できます。

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

C コアベースのアプリは、 [Server. Ports プロパティ](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)を使用して HTTPS を構成します。 同様の概念は、ASP.NET Core でサーバーを構成するために使用されます。 たとえば、Kestrel は、この機能に[エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)を使用します。

## <a name="interceptors-and-middleware"></a>インターセプターとミドルウェア

ASP.NET Core[ミドルウェア](xref:fundamentals/middleware/index)は、C コアベースの grpc アプリのインターセプターと比較して同様の機能を提供します。 ミドルウェアとインターセプターは、両方とも、gRPC 要求を処理するパイプラインを構築するために使用されるのと同じです。 どちらも、パイプライン内の次のコンポーネントの前または後に作業を実行できます。 ただし、ASP.NET Core ミドルウェアは基になる HTTP/2 メッセージに対して動作しますが、インターセプターは[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)を使用して、抽象の grpc レイヤーで動作します。

## <a name="additional-resources"></a>その他の資料

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
