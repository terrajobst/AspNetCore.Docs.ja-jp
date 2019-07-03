---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC のサービスを記述する場合は、基本的な概念を説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 5937ca9f2a783c4dabe324dae828b97953782938
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555861"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="47dc5-103">ASP.NET Core を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="47dc5-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="47dc5-104">このドキュメントでは、gRPC を ASP.NET Core を使用してサービスを開始する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="47dc5-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="47dc5-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47dc5-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47dc5-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47dc5-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47dc5-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47dc5-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="47dc5-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="47dc5-109">ASP.NET Core の gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="47dc5-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="47dc5-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="47dc5-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47dc5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47dc5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="47dc5-112">参照してください[gRPC サービスの使用開始](xref:tutorials/grpc/grpc-start)gRPC プロジェクトを作成する方法の詳細な手順についてはします。</span><span class="sxs-lookup"><span data-stu-id="47dc5-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="47dc5-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="47dc5-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="47dc5-114">コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="47dc5-115">GRPC サービス、ASP.NET Core アプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="47dc5-116">gRPC では、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="47dc5-116">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="47dc5-117">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="47dc5-117">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="47dc5-118">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) protobuf メッセージ Api。</span><span class="sxs-lookup"><span data-stu-id="47dc5-118">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="47dc5-119">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="47dc5-119">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="47dc5-120">GRPC を構成します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-120">Configure gRPC</span></span>

<span data-ttu-id="47dc5-121">gRPC、有効化され、`AddGrpc`メソッド。</span><span class="sxs-lookup"><span data-stu-id="47dc5-121">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="47dc5-122">各 gRPC サービスを通じてルーティング パイプラインに追加されます、`MapGrpcService`メソッド。</span><span class="sxs-lookup"><span data-stu-id="47dc5-122">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="47dc5-123">ASP.NET Core のミドルウェアと機能を共有して、ルーティングのパイプライン、ハンドラーの追加の要求を処理するためにそのため、アプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="47dc5-123">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="47dc5-124">MVC コント ローラーなど、追加の要求ハンドラーは、gRPC が構成されているサービスと並列で動作します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-124">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="47dc5-125">ASP.NET Core Api との統合</span><span class="sxs-lookup"><span data-stu-id="47dc5-125">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="47dc5-126">gRPC サービスが ASP.NET Core の機能へのフル アクセスをなどある[依存関係の注入](xref:fundamentals/dependency-injection)(DI) と[ログ](xref:fundamentals/logging/index)します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-126">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="47dc5-127">たとえば、サービスの実装では、コンス トラクターによって DI コンテナーからロガー サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="47dc5-127">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="47dc5-128">既定では、gRPC サービスの実装は、任意の有効期間 (シングルトン、スコープ ベース、または一時的) では、他の DI サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="47dc5-128">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="47dc5-129">GRPC メソッドで HttpContext を解決します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-129">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="47dc5-130">GRPC API は、メソッド、ホスト、ヘッダー、およびトレーラーなど、一部の http/2 メッセージ データへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-130">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="47dc5-131">アクセスはを介して、 `ServerCallContext` gRPC の各メソッドに渡される引数。</span><span class="sxs-lookup"><span data-stu-id="47dc5-131">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="47dc5-132">`ServerCallContext` フル アクセスを提供しない`HttpContext`すべての ASP.NET Api でします。</span><span class="sxs-lookup"><span data-stu-id="47dc5-132">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="47dc5-133">`GetHttpContext`拡張メソッドへのフル アクセスを提供する、 `HttpContext` ASP.NET Api では、基になる HTTP/2 メッセージを表します。</span><span class="sxs-lookup"><span data-stu-id="47dc5-133">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="47dc5-134">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="47dc5-134">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
