---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 496f659bd51e2404a936bea8aad77e674e1a285d
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022493"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="aa6ae-104">チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="aa6ae-105">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="aa6ae-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="aa6ae-106">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="aa6ae-107">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="aa6ae-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="aa6ae-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aa6ae-110">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="aa6ae-111">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="aa6ae-112">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aa6ae-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="aa6ae-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aa6ae-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa6ae-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa6ae-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="aa6ae-117">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="aa6ae-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aa6ae-119">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="aa6ae-120">**[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="aa6ae-121">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-121">Select **Next**</span></span>
* <span data-ttu-id="aa6ae-122">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="aa6ae-123">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="aa6ae-124">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-124">Select **Create**</span></span>
* <span data-ttu-id="aa6ae-125">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで:</span><span class="sxs-lookup"><span data-stu-id="aa6ae-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="aa6ae-126">ドロップダウン メニューで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="aa6ae-127">**gRPC サービス** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="aa6ae-128">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aa6ae-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa6ae-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aa6ae-130">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="aa6ae-131">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="aa6ae-132">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="aa6ae-133">`dotnet new` コマンドでは、*GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="aa6ae-134">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="aa6ae-135">"**ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="aa6ae-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="aa6ae-136">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="aa6ae-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa6ae-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aa6ae-138">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="aa6ae-139">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="aa6ae-140">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="aa6ae-140">Open the project</span></span>

<span data-ttu-id="aa6ae-141">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*GrpcGreeter.sln* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="aa6ae-142">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aa6ae-144">デバッガーなしで gRPC サービスを実行するには、`Ctrl+F5` キーを押します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="aa6ae-145">Visual Studio では、コマンド プロンプトでサービスが実行されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="aa6ae-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="aa6ae-147">`dotnet run` を使用し、コマンド ラインから gRPC あいさつプロジェクト *GrpcGreeter* を実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="aa6ae-148">サービスが `https://localhost:5001` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="aa6ae-149">gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="aa6ae-150">gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="aa6ae-151">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="aa6ae-152">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="aa6ae-153">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="aa6ae-154">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-154">Examine the project files</span></span>

<span data-ttu-id="aa6ae-155">*GrpcGreeter*プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="aa6ae-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="aa6ae-156">*greet.proto*:*Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="aa6ae-157">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="aa6ae-158">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="aa6ae-159">*appSettings.json*:Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="aa6ae-160">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="aa6ae-161">*Program.cs*:gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="aa6ae-162">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="aa6ae-163">*Startup.cs*:アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="aa6ae-164">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="aa6ae-165">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-165">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aa6ae-167">Visual Studio の 2 つ目のインスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-167">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="aa6ae-168">**[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** をメニュー バーから選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-168">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="aa6ae-169">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-169">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="aa6ae-170">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-170">Select **Next**</span></span>
* <span data-ttu-id="aa6ae-171">**[名前]** テキスト ボックスに、「GrpcGreeterClient」を入力します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-171">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="aa6ae-172">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-172">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aa6ae-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa6ae-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aa6ae-174">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-174">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="aa6ae-175">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-175">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="aa6ae-176">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-176">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa6ae-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aa6ae-178">[こちら](/dotnet/core/tutorials/using-on-mac-vs-full-solution)の指示に従い、*GrpcGreeterClient* という名前でコンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-178">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="aa6ae-179">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-179">Add required packages</span></span>

<span data-ttu-id="aa6ae-180">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-180">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="aa6ae-181">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="aa6ae-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="aa6ae-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="aa6ae-184">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-184">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-185">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa6ae-186">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-186">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="aa6ae-187">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="aa6ae-187">PMC option to install packages</span></span>

* <span data-ttu-id="aa6ae-188">Visual Studio で **[ツール]** 、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-188">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="aa6ae-189">**[パッケージ マネージャー コンソール]** ウィンドウから、*GrpcGreeterClient.csproj* ファイルがあるディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-189">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="aa6ae-190">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-190">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="aa6ae-191">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="aa6ae-191">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="aa6ae-192">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-192">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="aa6ae-193">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-193">Select the **Browse** tab.</span></span>
* <span data-ttu-id="aa6ae-194">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-194">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="aa6ae-195">**[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-195">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="aa6ae-196">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-196">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aa6ae-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa6ae-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aa6ae-198">**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-198">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa6ae-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aa6ae-200">**[Solution Pad]**  >  **[パッケージを追加]** で **[パッケージ]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-200">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="aa6ae-201">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-201">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="aa6ae-202">結果ウィンドウから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します</span><span class="sxs-lookup"><span data-stu-id="aa6ae-202">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="aa6ae-203">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-203">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="aa6ae-204">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-204">Add greet.proto</span></span>

* <span data-ttu-id="aa6ae-205">gRPC クライアント プロジェクトで **Protos** フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-205">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="aa6ae-206">gRPC あいさつサービスから gRPC クライアント プロジェクトに **Protos\greet.proto** ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-206">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="aa6ae-207">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-207">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-208">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="aa6ae-209">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-209">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aa6ae-210">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa6ae-210">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="aa6ae-211">*GrpcGreeterClient.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-211">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa6ae-212">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="aa6ae-213">プロジェクトを右クリックし、 **[ツール]、[ファイルの編集]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-213">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="aa6ae-214">**greet.proto** ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-214">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="aa6ae-215">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="aa6ae-215">Create the Greeter client</span></span>

<span data-ttu-id="aa6ae-216">プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-216">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="aa6ae-217">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-217">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="aa6ae-218">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-218">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="aa6ae-219">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-219">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="aa6ae-220">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-220">The Greeter client is created by:</span></span>

* <span data-ttu-id="aa6ae-221">gRPC サービスへの接続を作成するための情報が含まれている `HttpClient` をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-221">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="aa6ae-222">`HttpClient` を使用して、Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-222">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="aa6ae-223">Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-223">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="aa6ae-224">`SayHello` 呼び出しの結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-224">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="aa6ae-225">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="aa6ae-225">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa6ae-226">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa6ae-226">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aa6ae-227">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-227">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="aa6ae-228">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-228">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="aa6ae-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="aa6ae-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="aa6ae-230">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-230">Start the Greeter service.</span></span>
* <span data-ttu-id="aa6ae-231">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-231">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="aa6ae-232">クライアントがその名前 "GreeterClient" を含むメッセージを使用して、サービスにあいさつを送信します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-232">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="aa6ae-233">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-233">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="aa6ae-234">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-234">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="aa6ae-235">gRPC サービスは、成功した呼び出しの詳細をコマンド プロンプトに書き込まれるログに記録します。</span><span class="sxs-lookup"><span data-stu-id="aa6ae-235">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="aa6ae-236">次の手順</span><span class="sxs-lookup"><span data-stu-id="aa6ae-236">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
