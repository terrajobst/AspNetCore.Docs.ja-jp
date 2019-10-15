---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: このチュートリアルでは、ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/signalr
ms.openlocfilehash: 843416cf00c9241f8c05b1aba041ebbe7a05cf80
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037670"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="f0839-103">チュートリアル: ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="f0839-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="f0839-104">このチュートリアルでは、SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="f0839-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="f0839-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f0839-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0839-106">Web プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0839-106">Create a web project.</span></span>
> * <span data-ttu-id="f0839-107">SignalR クライアント ライブラリを追加する。</span><span class="sxs-lookup"><span data-stu-id="f0839-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="f0839-108">SignalR ハブを作成する。</span><span class="sxs-lookup"><span data-stu-id="f0839-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="f0839-109">SignalR を使用するようにプロジェクトを構成する。</span><span class="sxs-lookup"><span data-stu-id="f0839-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="f0839-110">任意のクライアントから、接続されているすべてのクライアントにメッセージを送信するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f0839-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="f0839-111">最後には、次のように動作するチャット アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-111">At the end, you'll have a working chat app:</span></span>

![SignalR のサンプル アプリ](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="f0839-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f0839-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f0839-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0839-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f0839-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0839-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f0839-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f0839-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="f0839-117">Web アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="f0839-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f0839-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0839-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="f0839-119">メニューから、 **[ファイル]、[新規プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="f0839-120">**[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="f0839-121">**[新しいプロジェクトの構成]** ダイアログで、プロジェクトに *SignalRChat* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="f0839-122">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="f0839-123">Razor Pages を使用するプロジェクトを作成するには **[Web アプリケーション]** を選択し、次に **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f0839-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0839-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="f0839-126">新しいプロジェクト フォルダーを作成するフォルダーで[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="f0839-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="f0839-127">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f0839-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f0839-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f0839-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f0839-129">メニューから、 **[ファイル]、[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="f0839-130">**[.NET Core] > [アプリ] > [Web アプリケーション]** の順に選択し ( **[Web アプリケーション (Model-View-Controller)]** は選択しないでください)、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="f0839-131">**[ターゲット フレームワーク]** が **.NET Core 3.0** に設定されていることを確認してから、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="f0839-132">プロジェクトに *SignalRChat* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f0839-133">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="f0839-133">Add the SignalR client library</span></span>

<span data-ttu-id="f0839-134">SignalR サーバー ライブラリは、ASP.NET Core 3.0 共有フレームワークに含まれています。</span><span class="sxs-lookup"><span data-stu-id="f0839-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="f0839-135">JavaScript クライアント ライブラリはプロジェクトに自動的に含まれません。</span><span class="sxs-lookup"><span data-stu-id="f0839-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="f0839-136">このチュートリアルでは、ライブラリ マネージャー (LibMan) を使用して *unpkg* からクライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="f0839-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="f0839-137">unpkg は、npm (Node.js パッケージ マネージャー) で見つかるものすべてを配信できるコンテンツ配信ネットワーク (CDN) です。</span><span class="sxs-lookup"><span data-stu-id="f0839-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f0839-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0839-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="f0839-139">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[追加]**  >  **[クライアント側のライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="f0839-140">**[Add Client-Side Library]\(クライアント側のライブラリの追加\)** ダイアログで、 **[プロバイダー]** に対して **[unpkg]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="f0839-141">**[ライブラリ]** に、「`@microsoft/signalr@latest`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f0839-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="f0839-142">**[Choose specific files]\(特定のファイルの選択\)** を選択して *dist/browser* フォルダーを展開し、*signalr.js* と *signalr.min.js* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="f0839-143">**[ターゲットの場所]** を *wwwroot/js/signalr/* に設定して、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![クライアント側のライブラリの追加ダイアログ - ライブラリの選択](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="f0839-145">LibMan によって *wwwroot/js/signalr* フォルダーが作成され、そこに選択したファイルがコピーされます。</span><span class="sxs-lookup"><span data-stu-id="f0839-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f0839-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0839-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="f0839-147">統合端末で、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f0839-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="f0839-148">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="f0839-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="f0839-149">出力が表示されるまでに数秒待機する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="f0839-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="f0839-150">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="f0839-151">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="f0839-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="f0839-152">ファイルを *wwwroot/js/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="f0839-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="f0839-153">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="f0839-153">Copy only the specified files.</span></span>

  <span data-ttu-id="f0839-154">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="f0839-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f0839-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f0839-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f0839-156">**[端末]** で、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f0839-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="f0839-157">プロジェクト フォルダー (ファイル *SignalRChat.csproj* を含んでいるフォルダー) に移動します。</span><span class="sxs-lookup"><span data-stu-id="f0839-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="f0839-158">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="f0839-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="f0839-159">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="f0839-160">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="f0839-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="f0839-161">ファイルを *wwwroot/js/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="f0839-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="f0839-162">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="f0839-162">Copy only the specified files.</span></span>

  <span data-ttu-id="f0839-163">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="f0839-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="f0839-164">SignalR ハブを作成する</span><span class="sxs-lookup"><span data-stu-id="f0839-164">Create a SignalR hub</span></span>

<span data-ttu-id="f0839-165">*ハブ*はクライアント サーバー通信を処理するハイレベル パイプラインとして機能するクラスです。</span><span class="sxs-lookup"><span data-stu-id="f0839-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="f0839-166">SignalRChat プロジェクト フォルダー内で、*Hubs* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0839-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="f0839-167">*Hubs* フォルダー内で、次のコードを使用して *ChatHub.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0839-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="f0839-168">`ChatHub` クラスは、SignalR `Hub` クラスを継承します。</span><span class="sxs-lookup"><span data-stu-id="f0839-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="f0839-169">`Hub` クラスでは、接続、グループ、およびメッセージングが管理されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="f0839-170">`SendMessage` メソッドは、メッセージをすべてのクライアントに送信するために、接続されたクライアントによって呼び出される場合があります。</span><span class="sxs-lookup"><span data-stu-id="f0839-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="f0839-171">このメソッドを呼び出す JavaScript クライアント コードは、チュートリアルの後半で示されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="f0839-172">最大のスケーラビリティを実現するために、SignalR コードは非同期になっています。</span><span class="sxs-lookup"><span data-stu-id="f0839-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="f0839-173">SignalR を構成する</span><span class="sxs-lookup"><span data-stu-id="f0839-173">Configure SignalR</span></span>

<span data-ttu-id="f0839-174">SignalR 要求が SignalR に渡されるように SignalR サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0839-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="f0839-175">次の強調表示されたコードを *Startup.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="f0839-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="f0839-176">これらの変更によって、SignalR が ASP.NET Core 依存関係挿入およびルーティング システムに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="f0839-177">SignalR クライアント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="f0839-177">Add SignalR client code</span></span>

* <span data-ttu-id="f0839-178">*Pages\Index.cshtml* のコンテンツを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="f0839-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="f0839-179">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="f0839-179">The preceding code:</span></span>

  * <span data-ttu-id="f0839-180">名前およびメッセージ テキスト用のテキスト ボックスと送信ボタンを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0839-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="f0839-181">SignalR ハブから受信したメッセージを表示するために、`id="messagesList"` を使用してリストを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0839-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="f0839-182">SignalR へのスクリプト参照と、次の手順で作成する *chat.js* アプリケーション コードを含めます。</span><span class="sxs-lookup"><span data-stu-id="f0839-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="f0839-183">*wwwroot/js* フォルダー内に、次のコードを使用して *chat.js* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0839-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="f0839-184">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="f0839-184">The preceding code:</span></span>

  * <span data-ttu-id="f0839-185">接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="f0839-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="f0839-186">ハブにメッセージを送信するハンドラーを送信ボタンに追加します。</span><span class="sxs-lookup"><span data-stu-id="f0839-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="f0839-187">ハブからメッセージを受信してからそれをリストに追加するハンドラーを接続オブジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="f0839-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="f0839-188">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="f0839-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f0839-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0839-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0839-190">**Ctrl + F5** キーを押して、デバッグを行わずにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f0839-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f0839-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0839-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f0839-192">統合端末で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f0839-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f0839-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f0839-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f0839-194">メニューから、 **[実行]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="f0839-195">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="f0839-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="f0839-196">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[Send Message]\(メッセージの送信\)** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f0839-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="f0839-197">次の瞬間、両方のページに名前とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR のサンプル アプリ](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="f0839-199">アプリが動作しない場合は、ご利用のブラウザーの開発者ツール (F12) を開き、コンソールに移動します。</span><span class="sxs-lookup"><span data-stu-id="f0839-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="f0839-200">HTML および JavaScript コードに関連するエラーが発生している場合があります。</span><span class="sxs-lookup"><span data-stu-id="f0839-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="f0839-201">たとえば、*signalr.js* を指示されたフォルダーとは別のフォルダーに配置したとします。</span><span class="sxs-lookup"><span data-stu-id="f0839-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="f0839-202">その場合、そのファイルへの参照は機能せず、コンソールに 404 エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f0839-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="f0839-203">![エラー "signalr.js が見つかりませんでした"](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="f0839-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="f0839-204">Chrome でエラー ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY が発生する場合は、次のコマンドを実行して開発証明書を更新します。</span><span class="sxs-lookup"><span data-stu-id="f0839-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="f0839-205">次の手順</span><span class="sxs-lookup"><span data-stu-id="f0839-205">Next steps</span></span>

<span data-ttu-id="f0839-206">SignalR の詳細については、次の概要を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f0839-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f0839-207">ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="f0839-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
