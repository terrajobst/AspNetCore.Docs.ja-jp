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
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="e9a49-102">Visual Studio を使用し、ASP.NET Core 3.0 で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="e9a49-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="e9a49-103">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="e9a49-104">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="e9a49-105">このチュートリアルでは、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="e9a49-105">In this tutorial, you;</span></span>

* <span data-ttu-id="e9a49-106">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="e9a49-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="e9a49-107">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-107">Create a gRPC client.</span></span>
* <span data-ttu-id="e9a49-108">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="e9a49-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="e9a49-109">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="e9a49-109">Create a gRPC service</span></span>

* <span data-ttu-id="e9a49-110">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e9a49-111">**[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e9a49-112">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-112">Select **Next**</span></span>
* <span data-ttu-id="e9a49-113">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="e9a49-114">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="e9a49-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="e9a49-115">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-115">Select **Create**</span></span>
* <span data-ttu-id="e9a49-116">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで:</span><span class="sxs-lookup"><span data-stu-id="e9a49-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="e9a49-117">ドロップダウン メニューで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="e9a49-118">**gRPC サービス** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="e9a49-119">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="e9a49-120">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="e9a49-120">Run the service</span></span>

* <span data-ttu-id="e9a49-121">デバッガーなしで gRPC サービスを実行するには、`Ctrl+F5` キーを押します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="e9a49-122">Visual Studio では、コマンド プロンプトでサービスが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="e9a49-123">サービスが `https://localhost:5001` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="e9a49-124">gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="e9a49-125">gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="e9a49-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="e9a49-126">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="e9a49-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="e9a49-127">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="e9a49-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="e9a49-128">詳細については、[macOS の gRPC と ASP.NET Core](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9a49-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="e9a49-129">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="e9a49-129">Examine the project files</span></span>

<span data-ttu-id="e9a49-130">*GrpcGreeter*プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="e9a49-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="e9a49-131">*greet.proto*:*Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="e9a49-132">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9a49-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="e9a49-133">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="e9a49-134">*appSettings.json*:Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="e9a49-135">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9a49-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="e9a49-136">*Program.cs*:gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="e9a49-137">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9a49-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="e9a49-138">*Startup.cs*:アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="e9a49-139">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9a49-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="e9a49-140">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="e9a49-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="e9a49-141">Visual Studio の 2 つ目のインスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="e9a49-142">**[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** をメニュー バーから選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="e9a49-143">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="e9a49-144">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-144">Select **Next**</span></span>
* <span data-ttu-id="e9a49-145">**[名前]** テキスト ボックスに、「GrpcGreeterClient」を入力します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="e9a49-146">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="e9a49-147">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="e9a49-147">Add required packages</span></span>

<span data-ttu-id="e9a49-148">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="e9a49-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="e9a49-149">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="e9a49-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="e9a49-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="e9a49-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="e9a49-152">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="e9a49-153">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="e9a49-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="e9a49-154">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="e9a49-154">PMC option to install packages</span></span>

* <span data-ttu-id="e9a49-155">Visual Studio で **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="e9a49-156">**[パッケージ マネージャー コンソール]** ウィンドウから、*GrpcGreeterClient.csproj* ファイルがあるディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="e9a49-157">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="e9a49-158">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="e9a49-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="e9a49-159">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e9a49-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="e9a49-160">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="e9a49-161">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="e9a49-162">**[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="e9a49-163">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="e9a49-164">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="e9a49-164">Add greet.proto</span></span>

* <span data-ttu-id="e9a49-165">gRPC クライアント プロジェクトで **Protos** フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="e9a49-166">gRPC あいさつサービスから gRPC クライアント プロジェクトに **Protos\greet.proto** ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="e9a49-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="e9a49-167">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="e9a49-168">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="e9a49-169">**greet.proto** ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="e9a49-170">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="e9a49-170">Create the Greeter client</span></span>

<span data-ttu-id="e9a49-171">プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="e9a49-172">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="e9a49-173">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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

<span data-ttu-id="e9a49-174">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e9a49-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="e9a49-175">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="e9a49-176">gRPC サービスへの接続を作成するための情報が含まれている `GrpcChannel` をインスタンス化する。</span><span class="sxs-lookup"><span data-stu-id="e9a49-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="e9a49-177">`GrpcChannel` を使用して、Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="e9a49-178">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="e9a49-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="e9a49-179">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="e9a49-180">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="e9a49-181">クライアントがその名前 "GreeterClient" を含むメッセージを使用して、サービスにあいさつを送信します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="e9a49-182">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="e9a49-183">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="e9a49-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="e9a49-184">gRPC サービスは、成功した呼び出しの詳細をコマンド プロンプトに書き込まれるログに記録します。</span><span class="sxs-lookup"><span data-stu-id="e9a49-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="e9a49-185">gRPC のドキュメント ヘルプと次の手順</span><span class="sxs-lookup"><span data-stu-id="e9a49-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="e9a49-186">ASP.NET Core の gRPC の概要</span><span class="sxs-lookup"><span data-stu-id="e9a49-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="e9a49-187">C# を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="e9a49-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="e9a49-188">C Core から ASP.NET Core に gRPC サービスを移行する</span><span class="sxs-lookup"><span data-stu-id="e9a49-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
