---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 8507c3dcefeb61a4dd34a1ff967c082d287cf646
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555887"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="29d60-104">チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="29d60-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="29d60-105">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="29d60-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="29d60-106">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="29d60-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="29d60-107">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="29d60-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="29d60-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="29d60-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="29d60-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="29d60-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="29d60-110">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="29d60-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="29d60-111">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="29d60-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="29d60-112">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="29d60-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="29d60-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="29d60-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="29d60-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29d60-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="29d60-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="29d60-117">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="29d60-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29d60-119">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="29d60-120">**[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="29d60-121">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-121">Select **Next**</span></span>
* <span data-ttu-id="29d60-122">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="29d60-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="29d60-123">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="29d60-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="29d60-124">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-124">Select **Create**</span></span>
* <span data-ttu-id="29d60-125">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで:</span><span class="sxs-lookup"><span data-stu-id="29d60-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="29d60-126">ドロップダウン メニューで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="29d60-127">**gRPC サービス** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="29d60-128">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="29d60-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29d60-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29d60-130">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="29d60-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="29d60-131">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="29d60-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="29d60-132">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="29d60-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="29d60-133">`dotnet new` コマンドでは、*GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="29d60-134">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="29d60-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="29d60-135">"**ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="29d60-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="29d60-136">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="29d60-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="29d60-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="29d60-138">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="29d60-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="29d60-139">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="29d60-140">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="29d60-140">Open the project</span></span>

<span data-ttu-id="29d60-141">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*GrpcGreeter.sln* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="29d60-142">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="29d60-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29d60-144">デバッガーなしで gRPC サービスを実行するには、`Ctrl+F5` キーを押します。</span><span class="sxs-lookup"><span data-stu-id="29d60-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="29d60-145">Visual Studio では、コマンド プロンプトでサービスが実行されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="29d60-146">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="29d60-147">`dotnet run` を使用し、コマンド ラインから gRPC あいさつプロジェクト *GrpcGreeter* を実行します。</span><span class="sxs-lookup"><span data-stu-id="29d60-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="29d60-148">サービスが `http://localhost:50051` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-148">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="29d60-149">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="29d60-149">Examine the project files</span></span>

<span data-ttu-id="29d60-150">*GrpcGreeter*プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="29d60-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="29d60-151">*greet.proto*:*Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="29d60-152">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29d60-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="29d60-153">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="29d60-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="29d60-154">*appSettings.json*:Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="29d60-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="29d60-155">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29d60-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="29d60-156">*Program.cs*:gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="29d60-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="29d60-157">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29d60-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="29d60-158">*Startup.cs*:アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="29d60-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="29d60-159">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="29d60-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="29d60-160">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="29d60-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29d60-162">Visual Studio の 2 つ目のインスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="29d60-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="29d60-163">**[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** をメニュー バーから選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="29d60-164">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="29d60-165">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-165">Select **Next**</span></span>
* <span data-ttu-id="29d60-166">**[名前]** テキスト ボックスに、「GrpcGreeterClient」を入力します。</span><span class="sxs-lookup"><span data-stu-id="29d60-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="29d60-167">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="29d60-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29d60-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="29d60-169">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="29d60-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="29d60-170">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="29d60-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="29d60-171">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="29d60-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="29d60-172">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="29d60-173">[こちら](/dotnet/core/tutorials/using-on-mac-vs-full-solution)の指示に従い、*GrpcGreeterClient* という名前でコンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="29d60-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="29d60-174">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="29d60-174">Add required packages</span></span>

<span data-ttu-id="29d60-175">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="29d60-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="29d60-176">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="29d60-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="29d60-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="29d60-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="29d60-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="29d60-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="29d60-179">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="29d60-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29d60-181">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="29d60-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="29d60-182">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="29d60-182">PMC option to install packages</span></span>

* <span data-ttu-id="29d60-183">Visual Studio で **[ツール]** 、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="29d60-184">**[パッケージ マネージャー コンソール]** ウィンドウから、*GrpcGreeterClient.csproj* ファイルがあるディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="29d60-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="29d60-185">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="29d60-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="29d60-186">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="29d60-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="29d60-187">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="29d60-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="29d60-188">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="29d60-189">検索ボックスに「**Grpc.Core**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="29d60-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="29d60-190">**[参照]** タブから **Grpc.Core** パッケージを選択し、 **[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="29d60-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="29d60-191">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="29d60-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="29d60-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29d60-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="29d60-193">**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="29d60-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="29d60-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="29d60-195">**[Solution Pad]**  >  **[パッケージを追加]** で **[パッケージ]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="29d60-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="29d60-196">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="29d60-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="29d60-197">結果ウィンドウから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します</span><span class="sxs-lookup"><span data-stu-id="29d60-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="29d60-198">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="29d60-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="29d60-199">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="29d60-199">Add greet.proto</span></span>

* <span data-ttu-id="29d60-200">gRPC クライアント プロジェクトで **Protos** フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="29d60-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="29d60-201">gRPC あいさつサービスから gRPC クライアント プロジェクトに **Protos\greet.proto** ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="29d60-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="29d60-202">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="29d60-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="29d60-204">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="29d60-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29d60-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="29d60-206">*GrpcGreeterClient.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="29d60-207">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="29d60-208">プロジェクトを右クリックし、 **[ツール]、[ファイルの編集]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="29d60-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="29d60-209">**greet.proto** ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="29d60-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="29d60-210">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="29d60-210">Create the Greeter client</span></span>

<span data-ttu-id="29d60-211">プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="29d60-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="29d60-212">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="29d60-213">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="29d60-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="29d60-214">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="29d60-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="29d60-215">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="29d60-216">gRPC サービスへの接続を作成するための情報が含まれている `HttpClient` をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="29d60-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="29d60-217">`HttpClient` を使用して、Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="29d60-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="29d60-218">Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="29d60-219">`SayHello` 呼び出しの結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="29d60-220">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="29d60-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29d60-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d60-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="29d60-222">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="29d60-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="29d60-223">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="29d60-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="29d60-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="29d60-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="29d60-225">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="29d60-225">Start the Greeter service.</span></span>
* <span data-ttu-id="29d60-226">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="29d60-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="29d60-227">クライアントがその名前 "GreeterClient" を含むメッセージを使用して、サービスにあいさつを送信します。</span><span class="sxs-lookup"><span data-stu-id="29d60-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="29d60-228">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="29d60-229">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="29d60-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="29d60-230">gRPC サービスは、成功した呼び出しの詳細をコマンド プロンプトに書き込まれるログに記録します。</span><span class="sxs-lookup"><span data-stu-id="29d60-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a><span data-ttu-id="29d60-231">次の手順</span><span class="sxs-lookup"><span data-stu-id="29d60-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
