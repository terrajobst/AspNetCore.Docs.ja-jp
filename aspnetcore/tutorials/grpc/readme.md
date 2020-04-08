---
page_type: sample
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648158"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Visual Studio を使用し、ASP.NET Core 3.0 で gRPC のクライアントとサーバーを作成する

このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。

最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。

このチュートリアルでは、次の作業を行います。

* gRPC サービスを作成する。
* gRPC クライアントを作成します。
* gRPC あいさつサービスで gRPC クライアント サービスをテストする。

## <a name="create-a-grpc-service"></a>gRPC サービスの作成

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。
* **[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** を選択します。
* **[次へ]** を選択します。
* プロジェクトに **GrpcGreeter** という名前を付けます。 コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。
* **[作成]** を選択します。
* **[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで:
  * ドロップダウン メニューで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択します。 
  * **gRPC サービス** テンプレートを選択します。
  * **[作成]** を選択します。

### <a name="run-the-service"></a>サービスを実行する

* デバッガーなしで gRPC サービスを実行するには、`Ctrl+F5` キーを押します。

  Visual Studio では、コマンド プロンプトでサービスが実行されます。

サービスが `https://localhost:5001` でリッスンしていることがログに示されます。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。 gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。
>
> macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。 macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。 詳細については、[macOS の gRPC と ASP.NET Core](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos) に関するページを参照してください。

### <a name="examine-the-project-files"></a>プロジェクト ファイルを確認する

*GrpcGreeter*プロジェクト ファイル:

* *greet.proto*:*Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。 詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。
* *Services* フォルダー:`Greeter` サービスの実装が含まれます。
* *appSettings.json*:Kestrel で使用されるプロトコルなどの構成データが含まれています。 詳細については、「<xref:fundamentals/configuration/index>」を参照してください。
* *Program.cs*:gRPC サービスのエントリ ポイントが含まれています。 詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。
* *Startup.cs*:アプリの動作を構成するコードが含まれています。 詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。

## <a name="create-the-grpc-client-in-a-net-console-app"></a>.NET コンソール アプリで gRPC クライアントを作成する

* Visual Studio の 2 つ目のインスタンスを開きます。
* **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** をメニュー バーから選択します。
* **[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択します。
* **[次へ]** を選択します。
* **[名前]** テキスト ボックスに、「GrpcGreeterClient」を入力します。
* **[作成]** を選択します。

### <a name="add-required-packages"></a>必要なパッケージを追加する

gRPC クライアント プロジェクトには、次のパッケージが必要です。

* .NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。 ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。

パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。

#### <a name="pmc-option-to-install-packages"></a>パッケージをインストールするための PMC オプション

* Visual Studio で **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。
* **[パッケージ マネージャー コンソール]** ウィンドウから、*GrpcGreeterClient.csproj* ファイルがあるディレクトリに移動します。
* 次のコマンドを実行します。

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>パッケージをインストールするための [NuGet パッケージの管理] オプション

* **[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。
* **[参照]** タブを選択します。
* 検索ボックスに「**Grpc.Net.Client**」と入力します。
* **[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。
* `Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。

### <a name="add-greetproto"></a>greet.proto を追加する

* gRPC クライアント プロジェクトで **Protos** フォルダーを作成します。
* gRPC あいさつサービスから gRPC クライアント プロジェクトに **Protos\greet.proto** ファイルをコピーします。
* *GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。

  プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。

* **greet.proto** ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter クライアントを作成する

プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。 `GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。

次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。

Greeter クライアントは、次の方法で作成されます。

* gRPC サービスへの接続を作成するための情報が含まれている `GrpcChannel` をインスタンス化する。
* `GrpcChannel` を使用して、Greeter クライアントを構築します。

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>gRPC あいさつサービスで gRPC クライアントをテストする

* あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。
* `GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。

クライアントがその名前 "GreeterClient" を含むメッセージを使用して、サービスにあいさつを送信します。 サービスから応答として "Hello GreeterClient" のメッセージが送信されます。 "Hello GreeterClient" の応答がコマンド プロンプトに表示されます。

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

gRPC サービスは、成功した呼び出しの詳細をコマンド プロンプトに書き込まれるログに記録します。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a>gRPC のドキュメント ヘルプと次の手順

* [ASP.NET Core の gRPC の概要](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [C# を使用した gRPC サービス](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [C Core から ASP.NET Core に gRPC サービスを移行する](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
