---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 8/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 8c0f57e69d4cdee5b5f5510d7db04991ed6df475
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250821"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="ac2c3-104">チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="ac2c3-105">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ac2c3-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ac2c3-106">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="ac2c3-107">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="ac2c3-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ac2c3-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac2c3-110">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="ac2c3-111">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="ac2c3-112">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ac2c3-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ac2c3-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="ac2c3-117">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="ac2c3-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac2c3-119">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="ac2c3-120">または、Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ac2c3-121">**[新しいプロジェクトの作成]** ダイアログで、 **[gRPC サービス]** を選択して、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![**[新しいプロジェクトの作成]** ダイアログ](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="ac2c3-123">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="ac2c3-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="ac2c3-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-125">Select **Create**.</span></span>
* <span data-ttu-id="ac2c3-126">**[Create a new gRPC service]\(新しい gPRC サービスの作成\)** ダイアログで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="ac2c3-127">**gRPC サービス** テンプレートが選択されています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="ac2c3-128">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac2c3-130">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ac2c3-131">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ac2c3-132">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="ac2c3-133">`dotnet new` コマンドでは、*GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="ac2c3-134">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="ac2c3-135">"**ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="ac2c3-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="ac2c3-136">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ac2c3-138">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="ac2c3-139">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="ac2c3-140">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="ac2c3-140">Open the project</span></span>

<span data-ttu-id="ac2c3-141">Visual Studio から、 **[ファイル]**  >  **[開く]** の順に選択し、*GrpcGreeter.sln* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.sln* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="ac2c3-142">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac2c3-144">デバッガーなしで gRPC サービスを実行するには、`Ctrl+F5` キーを押します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="ac2c3-145">Visual Studio では、コマンド プロンプトでサービスが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-146">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac2c3-147">`dotnet run` を使用し、コマンド ラインから gRPC あいさつプロジェクト *GrpcGreeter* を実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ac2c3-149">`dotnet run` を使用し、コマンド ラインから gRPC あいさつプロジェクト *GrpcGreeter* を実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-149">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

---

<span data-ttu-id="ac2c3-150">サービスが `https://localhost:5001` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-150">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="ac2c3-151">gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-151">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="ac2c3-152">gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-152">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="ac2c3-153">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-153">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ac2c3-154">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-154">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ac2c3-155">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-155">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="ac2c3-156">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-156">Examine the project files</span></span>

<span data-ttu-id="ac2c3-157">*GrpcGreeter*プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="ac2c3-157">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="ac2c3-158">*greet.proto* &ndash; *Protos/greet.proto* ファイルでは `Greeter` gRPC が定義されており、このファイルは gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-158">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="ac2c3-159">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-159">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="ac2c3-160">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="ac2c3-161">*appSettings.json* &ndash; Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-161">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="ac2c3-162">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="ac2c3-163">*Program.cs* &ndash; gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-163">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="ac2c3-164">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-164">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="ac2c3-165">*Startup.cs* &ndash; アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-165">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="ac2c3-166">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-166">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="ac2c3-167">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-167">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-168">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac2c3-169">Visual Studio のインスタンスをもう 1 つ開き、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-169">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="ac2c3-170">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-170">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="ac2c3-171">**[名前]** テキスト ボックスに「**GrpcGreeterClient**」を入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-171">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac2c3-173">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-173">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ac2c3-174">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-174">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ac2c3-175">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-175">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ac2c3-177">「[Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築](/dotnet/core/tutorials/using-on-mac-vs-full-solution)」の手順に従って、*GrpcGreeterClient* という名前のコンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-177">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="ac2c3-178">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-178">Add required packages</span></span>

<span data-ttu-id="ac2c3-179">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-179">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="ac2c3-180">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-180">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="ac2c3-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="ac2c3-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="ac2c3-183">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-183">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-184">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac2c3-185">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-185">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="ac2c3-186">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="ac2c3-186">PMC option to install packages</span></span>

* <span data-ttu-id="ac2c3-187">Visual Studio で **[ツール]** 、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-187">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="ac2c3-188">**[パッケージ マネージャー コンソール]** ウィンドウから `cd GrpcGreeterClient` を実行し、*GrpcGreeterClient.csproj* ファイルが含まれるフォルダーにディレクトリを変更します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-188">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="ac2c3-189">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-189">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="ac2c3-190">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="ac2c3-190">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="ac2c3-191">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-191">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="ac2c3-192">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-192">Select the **Browse** tab.</span></span>
* <span data-ttu-id="ac2c3-193">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ac2c3-194">**[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-194">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="ac2c3-195">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac2c3-197">**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-197">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-198">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ac2c3-199">**[Solution Pad]**  >  **[パッケージを追加]** で **[パッケージ]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-199">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="ac2c3-200">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-200">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ac2c3-201">結果ウィンドウから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ac2c3-201">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="ac2c3-202">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-202">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="ac2c3-203">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-203">Add greet.proto</span></span>

* <span data-ttu-id="ac2c3-204">gRPC クライアント プロジェクトで *Protos* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-204">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="ac2c3-205">gRPC あいさつサービスから gRPC クライアント プロジェクトに *Protos\greet.proto* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-205">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="ac2c3-206">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="ac2c3-208">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="ac2c3-210">*GrpcGreeterClient.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="ac2c3-212">プロジェクトを右クリックし、 **[ツール]**  >  **[ファイルの編集]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-212">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="ac2c3-213">*greet.proto* ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="ac2c3-214">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="ac2c3-214">Create the Greeter client</span></span>

<span data-ttu-id="ac2c3-215">プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-215">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="ac2c3-216">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="ac2c3-217">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="ac2c3-218">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="ac2c3-219">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="ac2c3-220">gRPC サービスへの接続を作成するための情報が含まれている `HttpClient` をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-220">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="ac2c3-221">`HttpClient` を使用して、gRPC チャネルと Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-221">Using the `HttpClient` to construct a gRPC channel and the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="ac2c3-222">Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="ac2c3-223">`SayHello` 呼び出しの結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="ac2c3-224">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="ac2c3-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ac2c3-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac2c3-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac2c3-226">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="ac2c3-227">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ac2c3-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac2c3-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ac2c3-229">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-229">Start the Greeter service.</span></span>
* <span data-ttu-id="ac2c3-230">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-230">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ac2c3-231">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ac2c3-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ac2c3-232">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-232">Start the Greeter service.</span></span>
* <span data-ttu-id="ac2c3-233">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-233">Start the client.</span></span>

---

<span data-ttu-id="ac2c3-234">クライアントにより、その名前 *GreeterClient* が含まれるあいさつメッセージが、サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-234">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="ac2c3-235">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-235">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="ac2c3-236">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-236">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="ac2c3-237">gRPC サービスにより、成功した呼び出しの詳細が、コマンド プロンプトに書き込まれるログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-237">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2c3-238">この記事のコードでは、gRPC サービスをセキュリティで保護するために、ASP.NET Core HTTPS 開発証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-238">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="ac2c3-239">クライアントが `The remote certificate is invalid according to the validation procedure.` というメッセージで失敗する場合、その開発証明書は信頼されていません。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-239">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="ac2c3-240">この問題を解決する手順については、「[Windows と macOS で ASP.NET Core HTTPS 開発証明書を信頼します](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac2c3-240">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

### <a name="next-steps"></a><span data-ttu-id="ac2c3-241">次の手順</span><span class="sxs-lookup"><span data-stu-id="ac2c3-241">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
