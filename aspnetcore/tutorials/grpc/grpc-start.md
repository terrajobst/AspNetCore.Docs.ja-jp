---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
ms.author: johluo
ms.date: 11/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: e5373d9abb9a770132e756843dbd15534dbe3356
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116104"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="9025c-104">チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="9025c-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="9025c-105">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="9025c-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="9025c-106">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="9025c-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="9025c-107">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="9025c-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="9025c-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9025c-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9025c-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="9025c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9025c-110">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="9025c-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="9025c-111">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="9025c-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="9025c-112">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="9025c-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9025c-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9025c-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9025c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9025c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9025c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9025c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9025c-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9025c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="9025c-117">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="9025c-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9025c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9025c-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9025c-119">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="9025c-120">または、Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9025c-121">**[新しいプロジェクトの作成]** ダイアログで、 **[gRPC サービス]** を選択して、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![**[新しいプロジェクトの作成]** ダイアログ](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="9025c-123">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9025c-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="9025c-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="9025c-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="9025c-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-125">Select **Create**.</span></span>
* <span data-ttu-id="9025c-126">**[Create a new gRPC service]\(新しい gPRC サービスの作成\)** ダイアログで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9025c-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="9025c-127">**gRPC サービス** テンプレートが選択されています。</span><span class="sxs-lookup"><span data-stu-id="9025c-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="9025c-128">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9025c-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9025c-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9025c-130">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="9025c-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9025c-131">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="9025c-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="9025c-132">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9025c-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="9025c-133">`dotnet new` コマンドでは、*GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="9025c-134">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="9025c-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="9025c-135">"**ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="9025c-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="9025c-136">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9025c-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9025c-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9025c-138">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9025c-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="9025c-139">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="9025c-140">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="9025c-140">Open the project</span></span>

<span data-ttu-id="9025c-141">Visual Studio から、 **[ファイル]**  >  **[開く]** の順に選択し、*GrpcGreeter.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="9025c-142">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="9025c-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="9025c-143">サービスが `https://localhost:5001` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="9025c-144">gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9025c-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9025c-145">gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="9025c-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="9025c-146">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="9025c-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="9025c-147">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="9025c-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9025c-148">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9025c-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="9025c-149">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="9025c-149">Examine the project files</span></span>

<span data-ttu-id="9025c-150">*GrpcGreeter*プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="9025c-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="9025c-151">*greet.proto* &ndash; *Protos/greet.proto* ファイルでは `Greeter` gRPC が定義されており、このファイルは gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="9025c-152">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9025c-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="9025c-153">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9025c-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="9025c-154">*appSettings.json* &ndash; Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9025c-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="9025c-155">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9025c-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="9025c-156">*Program.cs* &ndash; gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9025c-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="9025c-157">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9025c-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="9025c-158">*Startup.cs* &ndash; アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9025c-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="9025c-159">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9025c-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="9025c-160">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="9025c-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9025c-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9025c-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9025c-162">Visual Studio のインスタンスをもう 1 つ開き、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="9025c-163">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="9025c-164">**[名前]** テキスト ボックスに「**GrpcGreeterClient**」を入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9025c-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9025c-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9025c-166">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="9025c-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9025c-167">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="9025c-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="9025c-168">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9025c-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9025c-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9025c-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9025c-170">「[Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築](/dotnet/core/tutorials/using-on-mac-vs-full-solution)」の手順に従って、*GrpcGreeterClient* という名前のコンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9025c-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="9025c-171">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="9025c-171">Add required packages</span></span>

<span data-ttu-id="9025c-172">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="9025c-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="9025c-173">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="9025c-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="9025c-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9025c-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="9025c-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9025c-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="9025c-176">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="9025c-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9025c-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9025c-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9025c-178">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9025c-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="9025c-179">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="9025c-179">PMC option to install packages</span></span>

* <span data-ttu-id="9025c-180">Visual Studio で **[ツール]** 、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="9025c-181">**[パッケージ マネージャー コンソール]** ウィンドウから `cd GrpcGreeterClient` を実行し、*GrpcGreeterClient.csproj* ファイルが含まれるフォルダーにディレクトリを変更します。</span><span class="sxs-lookup"><span data-stu-id="9025c-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="9025c-182">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9025c-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="9025c-183">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="9025c-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="9025c-184">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9025c-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="9025c-185">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="9025c-186">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="9025c-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9025c-187">**[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="9025c-188">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="9025c-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9025c-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9025c-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9025c-190">**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9025c-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9025c-191">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9025c-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9025c-192">**[Solution Pad]**  >  **[パッケージを追加]** で **[パッケージ]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9025c-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="9025c-193">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="9025c-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9025c-194">結果ウィンドウから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します</span><span class="sxs-lookup"><span data-stu-id="9025c-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="9025c-195">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="9025c-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="9025c-196">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="9025c-196">Add greet.proto</span></span>

* <span data-ttu-id="9025c-197">gRPC クライアント プロジェクトで *Protos* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9025c-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="9025c-198">gRPC あいさつサービスから gRPC クライアント プロジェクトに *Protos\greet.proto* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="9025c-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="9025c-199">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="9025c-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9025c-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9025c-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="9025c-201">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9025c-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9025c-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="9025c-203">*GrpcGreeterClient.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9025c-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9025c-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="9025c-205">プロジェクトを右クリックし、 **[ツール]**  >  **[ファイルの編集]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9025c-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="9025c-206">*greet.proto* ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="9025c-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="9025c-207">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="9025c-207">Create the Greeter client</span></span>

<span data-ttu-id="9025c-208">プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="9025c-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="9025c-209">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="9025c-210">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="9025c-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="9025c-211">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9025c-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="9025c-212">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="9025c-213">gRPC サービスへの接続を作成するための情報が含まれている `GrpcChannel` をインスタンス化する。</span><span class="sxs-lookup"><span data-stu-id="9025c-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="9025c-214">`GrpcChannel` を使用して、Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="9025c-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="9025c-215">Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="9025c-216">`SayHello` 呼び出しの結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="9025c-217">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="9025c-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9025c-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9025c-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9025c-219">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9025c-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="9025c-220">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="9025c-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9025c-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9025c-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9025c-222">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9025c-222">Start the Greeter service.</span></span>
* <span data-ttu-id="9025c-223">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="9025c-223">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9025c-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9025c-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9025c-225">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9025c-225">Start the Greeter service.</span></span>
* <span data-ttu-id="9025c-226">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="9025c-226">Start the client.</span></span>

---

<span data-ttu-id="9025c-227">クライアントにより、その名前 *GreeterClient* が含まれるあいさつメッセージが、サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="9025c-228">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="9025c-229">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="9025c-230">gRPC サービスにより、成功した呼び出しの詳細が、コマンド プロンプトに書き込まれるログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="9025c-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="9025c-231">この記事のコードでは、gRPC サービスをセキュリティで保護するために、ASP.NET Core HTTPS 開発証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="9025c-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="9025c-232">クライアントが `The remote certificate is invalid according to the validation procedure.` というメッセージで失敗する場合、その開発証明書は信頼されていません。</span><span class="sxs-lookup"><span data-stu-id="9025c-232">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="9025c-233">この問題を解決する手順については、「[Windows と macOS で ASP.NET Core HTTPS 開発証明書を信頼します](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9025c-233">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="9025c-234">次の手順</span><span class="sxs-lookup"><span data-stu-id="9025c-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
