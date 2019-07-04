---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: このチュートリアルでは、ASP.NET Core SignalR を使用するチャット アプリを作成します。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 9a4296550a17ac2c348f2406e9f5b39877b02b59
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555927"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="b7dc6-103">チュートリアル: ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="b7dc6-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="b7dc6-104">このチュートリアルでは、SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="b7dc6-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b7dc6-106">Web プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-106">Create a web project.</span></span>
> * <span data-ttu-id="b7dc6-107">SignalR クライアント ライブラリを追加する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="b7dc6-108">SignalR ハブを作成する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="b7dc6-109">SignalR を使用するようにプロジェクトを構成する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="b7dc6-110">任意のクライアントから、接続されているすべてのクライアントにメッセージを送信するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="b7dc6-111">最後には、次のように動作するチャット アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-111">At the end, you'll have a working chat app:</span></span>

![SignalR のサンプル アプリ](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="b7dc6-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7dc6-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b7dc6-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7dc6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7dc6-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b7dc6-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7dc6-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b7dc6-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7dc6-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b7dc6-118">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b7dc6-118">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7dc6-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7dc6-119">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="b7dc6-120">メニューから、 **[ファイル]、[新規プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-120">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="b7dc6-121">**[新しいプロジェクト]** ダイアログで、 **[インストール]、[Visual C#]、[Web]、[ASP.NET Core Web アプリケーション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-121">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b7dc6-122">プロジェクトに "*SignalRChat*" という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-122">Name the project *SignalRChat*.</span></span>

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="b7dc6-124">**[Web アプリケーション]** を選択して、Razor Pages を使用するプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-124">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="b7dc6-125">**.NET Core** のターゲット フレームワークを選択し、**ASP.NET Core 2.2** を選択して、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-125">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b7dc6-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7dc6-127">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="b7dc6-128">新しいプロジェクト フォルダーを作成するフォルダーで[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-128">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="b7dc6-129">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-129">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b7dc6-130">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7dc6-130">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7dc6-131">メニューから、 **[ファイル]、[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-131">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="b7dc6-132">**[.NET Core]、[アプリ]、[ASP.NET Core Web アプリ]** の順に選択します ( **[ASP.NET Core Web アプリ (MVC)]** を選択しないでください)。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-132">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="b7dc6-133">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-133">Select **Next**.</span></span>

* <span data-ttu-id="b7dc6-134">プロジェクトに *SignalRChat* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-134">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="b7dc6-135">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="b7dc6-135">Add the SignalR client library</span></span>

<span data-ttu-id="b7dc6-136">SignalR サーバー ライブラリは、`Microsoft.AspNetCore.App` メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-136">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="b7dc6-137">JavaScript クライアント ライブラリはプロジェクトに自動的に含まれません。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-137">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="b7dc6-138">このチュートリアルでは、ライブラリ マネージャー (LibMan) を使用して *unpkg* からクライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-138">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="b7dc6-139">unpkg は、npm (Node.js パッケージ マネージャー) で見つかるものすべてを配信できるコンテンツ配信ネットワーク (CDN) です。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-139">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7dc6-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7dc6-140">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="b7dc6-141">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[追加]**  >  **[クライアント側のライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-141">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="b7dc6-142">**[Add Client-Side Library]\(クライアント側のライブラリの追加\)** ダイアログで、 **[プロバイダー]** に対して **[unpkg]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-142">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="b7dc6-143">**[ライブラリ]** に対して「`@aspnet/signalr@1`」を入力し、プレビューでない最新のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-143">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![クライアント側のライブラリの追加ダイアログ - ライブラリの選択](signalr/_static/libman1.png)

* <span data-ttu-id="b7dc6-145">**[Choose specific files]\(特定のファイルの選択\)** を選択して *dist/browser* フォルダーを展開し、*signalr.js* と *signalr.min.js* を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-145">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="b7dc6-146">**[ターゲットの場所]** を *wwwroot/lib/signalr/* に設定して、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-146">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![クライアント側のライブラリの追加ダイアログ - ファイルと保存先の選択](signalr/_static/libman2.png)

  <span data-ttu-id="b7dc6-148">LibMan によって *wwwroot/lib/signalr* フォルダーが作成され、そこに選択したファイルがコピーされます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-148">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b7dc6-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7dc6-149">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="b7dc6-150">統合端末で、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-150">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="b7dc6-151">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-151">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="b7dc6-152">出力が表示されるまでに数秒待機する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-152">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="b7dc6-153">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-153">The parameters specify the following options:</span></span>
  * <span data-ttu-id="b7dc6-154">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-154">Use the unpkg provider.</span></span>
  * <span data-ttu-id="b7dc6-155">ファイルを *wwwroot/lib/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-155">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="b7dc6-156">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-156">Copy only the specified files.</span></span>

  <span data-ttu-id="b7dc6-157">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-157">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b7dc6-158">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7dc6-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7dc6-159">**[端末]** で、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-159">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="b7dc6-160">プロジェクト フォルダー (ファイル *SignalRChat.csproj* を含んでいるフォルダー) に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-160">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="b7dc6-161">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-161">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="b7dc6-162">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-162">The parameters specify the following options:</span></span>
  * <span data-ttu-id="b7dc6-163">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-163">Use the unpkg provider.</span></span>
  * <span data-ttu-id="b7dc6-164">ファイルを *wwwroot/lib/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-164">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="b7dc6-165">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-165">Copy only the specified files.</span></span>

  <span data-ttu-id="b7dc6-166">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-166">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="b7dc6-167">SignalR ハブを作成する</span><span class="sxs-lookup"><span data-stu-id="b7dc6-167">Create a SignalR hub</span></span>

<span data-ttu-id="b7dc6-168">*ハブ*はクライアント サーバー通信を処理するハイレベル パイプラインとして機能するクラスです。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-168">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="b7dc6-169">SignalRChat プロジェクト フォルダー内で、*Hubs* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-169">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="b7dc6-170">*Hubs* フォルダー内で、次のコードを使用して *ChatHub.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-170">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="b7dc6-171">`ChatHub` クラスは、SignalR `Hub` クラスを継承します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-171">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="b7dc6-172">`Hub` クラスでは、接続、グループ、およびメッセージングが管理されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-172">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="b7dc6-173">`SendMessage` メソッドは、メッセージをすべてのクライアントに送信するために、接続されたクライアントによって呼び出される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-173">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="b7dc6-174">このメソッドを呼び出す JavaScript クライアント コードは、チュートリアルの後半で示されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-174">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="b7dc6-175">最大のスケーラビリティを実現するために、SignalR コードは非同期になっています。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-175">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="b7dc6-176">SignalR を構成する</span><span class="sxs-lookup"><span data-stu-id="b7dc6-176">Configure SignalR</span></span>

<span data-ttu-id="b7dc6-177">SignalR 要求が SignalR に渡されるように SignalR サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-177">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="b7dc6-178">次の強調表示されたコードを *Startup.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-178">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="b7dc6-179">これらの変更によって、SignalR が ASP.NET Core 依存関係挿入システムとミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-179">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="b7dc6-180">SignalR クライアント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="b7dc6-180">Add SignalR client code</span></span>

* <span data-ttu-id="b7dc6-181">*Pages\Index.cshtml* のコンテンツを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-181">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="b7dc6-182">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-182">The preceding code:</span></span>

  * <span data-ttu-id="b7dc6-183">名前およびメッセージ テキスト用のテキスト ボックスと送信ボタンを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-183">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="b7dc6-184">SignalR ハブから受信したメッセージを表示するために、`id="messagesList"` を使用してリストを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-184">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="b7dc6-185">SignalR へのスクリプト参照と、次の手順で作成する *chat.js* アプリケーション コードを含めます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-185">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="b7dc6-186">*wwwroot/js* フォルダー内に、次のコードを使用して *chat.js* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-186">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="b7dc6-187">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-187">The preceding code:</span></span>

  * <span data-ttu-id="b7dc6-188">接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-188">Creates and starts a connection.</span></span>
  * <span data-ttu-id="b7dc6-189">ハブにメッセージを送信するハンドラーを送信ボタンに追加します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-189">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="b7dc6-190">ハブからメッセージを受信してからそれをリストに追加するハンドラーを接続オブジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-190">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="b7dc6-191">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="b7dc6-191">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7dc6-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7dc6-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7dc6-193">**Ctrl + F5** キーを押して、デバッグを行わずにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-193">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b7dc6-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7dc6-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b7dc6-195">統合端末で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-195">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b7dc6-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7dc6-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7dc6-197">メニューから、 **[実行]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-197">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="b7dc6-198">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-198">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="b7dc6-199">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[Send Message]\(メッセージの送信\)** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-199">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="b7dc6-200">次の瞬間、両方のページに名前とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-200">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR のサンプル アプリ](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="b7dc6-202">アプリが動作しない場合は、ご利用のブラウザーの開発者ツール (F12) を開き、コンソールに移動します。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-202">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="b7dc6-203">HTML および JavaScript コードに関連するエラーが発生している場合があります。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-203">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="b7dc6-204">たとえば、*signalr.js* を指示されたフォルダーとは別のフォルダーに配置したとします。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-204">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="b7dc6-205">その場合、そのファイルへの参照は機能せず、コンソールに 404 エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-205">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="b7dc6-206">![エラー "signalr.js が見つかりませんでした"](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="b7dc6-206">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="b7dc6-207">次の手順</span><span class="sxs-lookup"><span data-stu-id="b7dc6-207">Next steps</span></span>

<span data-ttu-id="b7dc6-208">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-208">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b7dc6-209">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-209">Create a web app project.</span></span>
> * <span data-ttu-id="b7dc6-210">SignalR クライアント ライブラリを追加する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-210">Add the SignalR client library.</span></span>
> * <span data-ttu-id="b7dc6-211">SignalR ハブを作成する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-211">Create a SignalR hub.</span></span>
> * <span data-ttu-id="b7dc6-212">SignalR を使用するようにプロジェクトを構成する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-212">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="b7dc6-213">ハブを使用して任意のクライアントから接続済みのすべてのクライアントにメッセージを送信する、コードを追加する。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-213">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="b7dc6-214">SignalR の詳細については、次の概要を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7dc6-214">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b7dc6-215">ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="b7dc6-215">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
