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
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core を使用した gRPC サービス

このドキュメントでは、gRPC を ASP.NET Core を使用してサービスを開始する方法を示します。

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>ASP.NET Core の gRPC サービスの概要

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

参照してください[gRPC サービスの使用開始](xref:tutorials/grpc/grpc-start)gRPC プロジェクトを作成する方法の詳細な手順についてはします。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>GRPC サービス、ASP.NET Core アプリを追加します。

gRPC では、次のパッケージが必要です。

* [Grpc.AspNetCore.Server](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) protobuf メッセージ Api。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a>GRPC を構成します。

gRPC、有効化され、`AddGrpc`メソッド。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

各 gRPC サービスを通じてルーティング パイプラインに追加されます、`MapGrpcService`メソッド。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.NET Core のミドルウェアと機能を共有して、ルーティングのパイプライン、ハンドラーの追加の要求を処理するためにそのため、アプリを構成できます。 MVC コント ローラーなど、追加の要求ハンドラーは、gRPC が構成されているサービスと並列で動作します。

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Core Api との統合

gRPC サービスが ASP.NET Core の機能へのフル アクセスをなどある[依存関係の注入](xref:fundamentals/dependency-injection)(DI) と[ログ](xref:fundamentals/logging/index)します。 たとえば、サービスの実装では、コンス トラクターによって DI コンテナーからロガー サービスを解決できます。

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

既定では、gRPC サービスの実装は、任意の有効期間 (シングルトン、スコープ ベース、または一時的) では、他の DI サービスを解決できます。

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC メソッドで HttpContext を解決します。

GRPC API は、メソッド、ホスト、ヘッダー、およびトレーラーなど、一部の http/2 メッセージ データへのアクセスを提供します。 アクセスはを介して、 `ServerCallContext` gRPC の各メソッドに渡される引数。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` フル アクセスを提供しない`HttpContext`すべての ASP.NET Api でします。 `GetHttpContext`拡張メソッドへのフル アクセスを提供する、 `HttpContext` ASP.NET Api では、基になる HTTP/2 メッセージを表します。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
