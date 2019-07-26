---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 3e90e3b17186757fe157fb6641888786bb7a0df2
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412525"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する

作成者: [John Luo](https://github.com/juntaoluo)

このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。

最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * gRPC サービスを作成する。
> * gRPC クライアントを作成します。
> * gRPC あいさつサービスで gRPC クライアント サービスをテストする。

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>gRPC サービスの作成

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。
* **[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** を選択します。
* **[次へ]** を選択します。
* プロジェクトに **GrpcGreeter** という名前を付けます。 コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。
* **[作成]** を選択します。
* **[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで:
  * ドロップダウン メニューで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択します。 
  * **gRPC サービス** テンプレートを選択します。
  * **[作成]** を選択します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。
* ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。
* 次のコマンドを実行します。

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new` コマンドでは、*GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。
  * `code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。

  "**ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、
* **[はい]** を選択します

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

端末から、次のコマンドを実行します。

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、gRPC サービスが作成されます。

### <a name="open-the-project"></a>プロジェクトを開く

Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*GrpcGreeter.sln* ファイルを選択します。

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a>サービスを実行する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* デバッガーなしで gRPC サービスを実行するには、`Ctrl+F5` キーを押します。

  Visual Studio では、コマンド プロンプトでサービスが実行されます。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* `dotnet run` を使用し、コマンド ラインから gRPC あいさつプロジェクト *GrpcGreeter* を実行します。

<!-- End of combined VS/Mac tabs -->

---

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

### <a name="examine-the-project-files"></a>プロジェクト ファイルを確認する

*GrpcGreeter*プロジェクト ファイル:

* *greet.proto*:*Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。 詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。
* *Services* フォルダー:`Greeter` サービスの実装が含まれます。
* *appSettings.json*:Kestrel で使用されるプロトコルなどの構成データが含まれています。 詳細については、<xref:fundamentals/configuration/index> を参照してください。
* *Program.cs*:gRPC サービスのエントリ ポイントが含まれています。 詳細については、<xref:fundamentals/host/generic-host> を参照してください。
* *Startup.cs*:アプリの動作を構成するコードが含まれています。 詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。

## <a name="create-the-grpc-client-in-a-net-console-app"></a>.NET コンソール アプリで gRPC クライアントを作成する

## <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の 2 つ目のインスタンスを開きます。
* **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** をメニュー バーから選択します。
* **[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択します。
* **[次へ]** を選択します。
* **[名前]** テキスト ボックスに、「GrpcGreeterClient」を入力します。
* **[作成]** を選択します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。
* ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。
* 次のコマンドを実行します。

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[こちら](/dotnet/core/tutorials/using-on-mac-vs-full-solution)の指示に従い、*GrpcGreeterClient* という名前でコンソール アプリを作成します。

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a>必要なパッケージを追加する

gRPC クライアント プロジェクトには、次のパッケージが必要です。

* .NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。 ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。

#### <a name="pmc-option-to-install-packages"></a>パッケージをインストールするための PMC オプション

* Visual Studio で **[ツール]** 、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順に選択します。
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
* 検索ボックスに「**Grpc.Core**」と入力します。
* **[参照]** タブから **Grpc.Core** パッケージを選択し、 **[インストール]** をクリックします。
* `Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**統合ターミナル**から次のコマンドを実行します。

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[Solution Pad]**  >  **[パッケージを追加]** で **[パッケージ]** フォルダーを右クリックします。
* 検索ボックスに「**Grpc.Net.Client**」と入力します。
* 結果ウィンドウから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します
* `Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。

---

### <a name="add-greetproto"></a>greet.proto を追加する

* gRPC クライアント プロジェクトで **Protos** フォルダーを作成します。
* gRPC あいさつサービスから gRPC クライアント プロジェクトに **Protos\greet.proto** ファイルをコピーします。
* *GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

  プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

  *GrpcGreeterClient.csproj* ファイルを選択します。

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  プロジェクトを右クリックし、 **[ツール]、[ファイルの編集]** の順に選択します。

  ---

* **greet.proto** ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter クライアントを作成する

プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。 `GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。

次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。

Greeter クライアントは、次の方法で作成されます。

* gRPC サービスへの接続を作成するための情報が含まれている `HttpClient` をインスタンス化します。
* `HttpClient` を使用して、Greeter クライアントを構築します。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。 `SayHello` 呼び出しの結果が表示されます。

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>gRPC あいさつサービスで gRPC クライアントをテストする

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。
* `GrpcGreeterClient` プロジェクトで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* あいさつサービスを開始します。
* クライアントを起動します。

<!-- End of combined VS/Mac tabs -->

---

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

### <a name="next-steps"></a>次の手順

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
