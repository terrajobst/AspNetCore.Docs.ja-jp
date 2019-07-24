---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC サービスを作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 02e443dfecf2f7464a8ecabfc0cac67854d63232
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412481"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core を使用した gRPC サービス

このドキュメントでは、ASP.NET Core を使用して gRPC サービスを開始する方法について説明します。

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

GRPC プロジェクトを作成する方法の詳細については、「 [grpc サービスの概要](xref:tutorials/grpc/grpc-start)」を参照してください。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>ASP.NET Core アプリに gRPC サービスを追加する

gRPC には[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)パッケージが必要です。

### <a name="configure-grpc"></a>GRPC の構成

grpc は、 `AddGrpc`次の方法で有効になります。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

各 grpc サービスは、メソッドを`MapGrpcService`使用してルーティングパイプラインに追加されます。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.NET Core ミドルウェアと features はルーティングパイプラインを共有するため、追加の要求ハンドラーを提供するようにアプリを構成できます。 MVC コントローラーなどの追加の要求ハンドラーは、構成されている gRPC サービスと並行して動作します。

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Core Api との統合

gRPC サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) や[ログ記録](xref:fundamentals/logging/index)などの ASP.NET Core 機能へのフルアクセスがあります。 たとえば、サービス実装は、コンストラクターを使用して DI コンテナーから logger サービスを解決できます。

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

既定では、gRPC サービス実装は、任意の有効期間 (シングルトン、スコープ、または一時的) を持つ他の DI サービスを解決できます。

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC メソッドでの HttpContext の解決

GRPC API は、メソッド、ホスト、ヘッダー、トレーラーなど、一部の HTTP/2 メッセージデータへのアクセスを提供します。 アクセスは、各`ServerCallContext` grpc メソッドに渡される引数を使用して行われます。

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`では、すべての ASP.NET `HttpContext` api でへのフルアクセスが提供されるわけではありません。 拡張`GetHttpContext`メソッドは、ASP.NET api の基`HttpContext`になる HTTP/2 メッセージを表すへのフルアクセスを提供します。

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a>その他の資料

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
