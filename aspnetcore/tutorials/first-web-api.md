---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 17e8ee08fca775b8fccc3f2e6cd6067caca9c79f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207778"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4dd64-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="4dd64-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4dd64-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4dd64-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4dd64-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="4dd64-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4dd64-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-107">Create a web API project.</span></span>
> * <span data-ttu-id="4dd64-108">モデル クラスを追加する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-108">Add a model class.</span></span>
> * <span data-ttu-id="4dd64-109">データベース コンテキストを作成する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-109">Create the database context.</span></span>
> * <span data-ttu-id="4dd64-110">データベース コンテキストを登録する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-110">Register the database context.</span></span>
> * <span data-ttu-id="4dd64-111">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-111">Add a controller.</span></span>
> * <span data-ttu-id="4dd64-112">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="4dd64-113">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4dd64-114">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-114">Specify return values.</span></span>
> * <span data-ttu-id="4dd64-115">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="4dd64-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4dd64-116">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="4dd64-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="4dd64-117">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4dd64-118">概要</span><span class="sxs-lookup"><span data-stu-id="4dd64-118">Overview</span></span>

<span data-ttu-id="4dd64-119">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4dd64-120">API</span><span class="sxs-lookup"><span data-stu-id="4dd64-120">API</span></span> | <span data-ttu-id="4dd64-121">説明</span><span class="sxs-lookup"><span data-stu-id="4dd64-121">Description</span></span> | <span data-ttu-id="4dd64-122">要求本文</span><span class="sxs-lookup"><span data-stu-id="4dd64-122">Request body</span></span> | <span data-ttu-id="4dd64-123">応答本文</span><span class="sxs-lookup"><span data-stu-id="4dd64-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4dd64-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="4dd64-124">GET /api/todo</span></span> | <span data-ttu-id="4dd64-125">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-125">Get all to-do items</span></span> | <span data-ttu-id="4dd64-126">なし</span><span class="sxs-lookup"><span data-stu-id="4dd64-126">None</span></span> | <span data-ttu-id="4dd64-127">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="4dd64-127">Array of to-do items</span></span>|
|<span data-ttu-id="4dd64-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="4dd64-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="4dd64-129">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-129">Get an item by ID</span></span> | <span data-ttu-id="4dd64-130">なし</span><span class="sxs-lookup"><span data-stu-id="4dd64-130">None</span></span> | <span data-ttu-id="4dd64-131">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="4dd64-131">To-do item</span></span>|
|<span data-ttu-id="4dd64-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="4dd64-132">POST /api/todo</span></span> | <span data-ttu-id="4dd64-133">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-133">Add a new item</span></span> | <span data-ttu-id="4dd64-134">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="4dd64-134">To-do item</span></span> | <span data-ttu-id="4dd64-135">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="4dd64-135">To-do item</span></span> |
|<span data-ttu-id="4dd64-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="4dd64-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="4dd64-137">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="4dd64-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4dd64-138">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="4dd64-138">To-do item</span></span> | <span data-ttu-id="4dd64-139">なし</span><span class="sxs-lookup"><span data-stu-id="4dd64-139">None</span></span> |
|<span data-ttu-id="4dd64-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4dd64-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4dd64-141">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4dd64-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4dd64-142">なし</span><span class="sxs-lookup"><span data-stu-id="4dd64-142">None</span></span> | <span data-ttu-id="4dd64-143">なし</span><span class="sxs-lookup"><span data-stu-id="4dd64-143">None</span></span>|

<span data-ttu-id="4dd64-144">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="4dd64-144">The following diagram shows the design of the app.</span></span>

![クライアントは左側のボックスで表され、要求を送信し、アプリケーション (右側に描画されたボックス) から応答を受信します。](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="4dd64-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4dd64-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dd64-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dd64-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4dd64-151">**[ファイル]** メニューで、 **[新規作成]** 、 >  **[プロジェクト]** の順に作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4dd64-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="4dd64-153">プロジェクトに「*TodoApi*」という名前を付け、 **[OK]** をクリックます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="4dd64-154">**[New ASP.NET Core Web Application - TodoApi]\(新しい ASP.NET Core Web アプリケーション - TodoApi\)** ダイアログで、ASP.NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="4dd64-155">**API** テンプレートを選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="4dd64-156">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-156">Do **not** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4dd64-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dd64-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4dd64-159">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4dd64-160">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="4dd64-161">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4dd64-162">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4dd64-163">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4dd64-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4dd64-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4dd64-165">**[ファイル]** 、 **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-165">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4dd64-167">**[.NET Core アプリ]** 、 >  **[ASP.NET Core Web API]** 、 >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4dd64-169">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4dd64-170">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4dd64-172">API のテスト</span><span class="sxs-lookup"><span data-stu-id="4dd64-172">Test the API</span></span>

<span data-ttu-id="4dd64-173">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-173">The project template creates a `values` API.</span></span> <span data-ttu-id="4dd64-174">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dd64-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dd64-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dd64-176">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4dd64-177">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4dd64-178">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4dd64-179">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4dd64-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dd64-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dd64-181">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4dd64-182">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="4dd64-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4dd64-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4dd64-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4dd64-184">**[実行]** 、 **[デバッグありで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="4dd64-185">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4dd64-186">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4dd64-187">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="4dd64-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4dd64-188">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4dd64-189">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-189">Add a model class</span></span>

<span data-ttu-id="4dd64-190">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="4dd64-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4dd64-191">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="4dd64-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dd64-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dd64-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4dd64-193">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4dd64-194">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4dd64-195">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="4dd64-196">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4dd64-197">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4dd64-198">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4dd64-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dd64-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4dd64-200">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4dd64-201">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4dd64-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4dd64-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4dd64-203">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-203">Right-click the project.</span></span> <span data-ttu-id="4dd64-204">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4dd64-205">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-205">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4dd64-207">*Models* フォルダーを右クリックし、 **[追加]** 、 **[新しいファイル]** 、 **[全般]** 、 **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4dd64-208">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4dd64-209">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4dd64-210">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4dd64-211">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4dd64-212">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-212">Add a database context</span></span>

<span data-ttu-id="4dd64-213">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="4dd64-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4dd64-214">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dd64-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dd64-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4dd64-216">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4dd64-217">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4dd64-218">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4dd64-218">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4dd64-219">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-219">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4dd64-220">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-220">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4dd64-221">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="4dd64-221">Register the database context</span></span>

<span data-ttu-id="4dd64-222">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-222">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4dd64-223">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-223">The container provides the service to controllers.</span></span>

<span data-ttu-id="4dd64-224">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-224">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4dd64-225">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-225">The preceding code:</span></span>

* <span data-ttu-id="4dd64-226">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-226">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4dd64-227">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-227">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4dd64-228">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-228">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4dd64-229">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-229">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dd64-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dd64-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4dd64-231">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-231">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4dd64-232">**[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-232">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4dd64-233">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-233">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4dd64-234">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-234">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4dd64-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4dd64-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4dd64-237">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-237">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4dd64-238">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-238">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4dd64-239">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-239">The preceding code:</span></span>

* <span data-ttu-id="4dd64-240">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-240">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4dd64-241">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-241">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4dd64-242">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-242">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4dd64-243">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-243">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4dd64-244">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-244">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4dd64-245">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-245">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4dd64-246">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-246">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4dd64-247">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-247">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4dd64-248">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-248">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4dd64-249">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-249">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4dd64-250">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-250">Add Get methods</span></span>

<span data-ttu-id="4dd64-251">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-251">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4dd64-252">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-252">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4dd64-253">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-253">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4dd64-254">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-254">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4dd64-255">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-255">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4dd64-256">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="4dd64-256">Routing and URL paths</span></span>

<span data-ttu-id="4dd64-257">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-257">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4dd64-258">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-258">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4dd64-259">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-259">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4dd64-260">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-260">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4dd64-261">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="4dd64-261">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4dd64-262">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="4dd64-262">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4dd64-263">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-263">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4dd64-264">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="4dd64-264">This sample doesn't use a template.</span></span> <span data-ttu-id="4dd64-265">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-265">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4dd64-266">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="4dd64-266">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4dd64-267">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-267">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4dd64-268">戻り値</span><span class="sxs-lookup"><span data-stu-id="4dd64-268">Return values</span></span>

<span data-ttu-id="4dd64-269">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="4dd64-269">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4dd64-270">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-270">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4dd64-271">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-271">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4dd64-272">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-272">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4dd64-273">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-273">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4dd64-274">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-274">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4dd64-275">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-275">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4dd64-276">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-276">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4dd64-277">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-277">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4dd64-278">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="4dd64-278">Test the GetTodoItems method</span></span>

<span data-ttu-id="4dd64-279">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-279">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4dd64-280">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-280">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4dd64-281">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-281">Start the web app.</span></span>
* <span data-ttu-id="4dd64-282">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-282">Start Postman.</span></span>
* <span data-ttu-id="4dd64-283">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-283">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="4dd64-284">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-284">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4dd64-285">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-285">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4dd64-286">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-286">Create a new request.</span></span>
  * <span data-ttu-id="4dd64-287">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-287">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4dd64-288">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-288">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4dd64-289">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-289">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4dd64-290">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-290">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4dd64-291">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-291">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4dd64-293">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-293">Add a Create method</span></span>

<span data-ttu-id="4dd64-294">次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-294">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4dd64-295">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="4dd64-295">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4dd64-296">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-296">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4dd64-297">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="4dd64-297">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4dd64-298">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-298">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4dd64-299">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="4dd64-299">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4dd64-300">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-300">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4dd64-301">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-301">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4dd64-302">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-302">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4dd64-303">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-303">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4dd64-304">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="4dd64-304">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4dd64-305">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="4dd64-305">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4dd64-306">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-306">Build the project.</span></span>
* <span data-ttu-id="4dd64-307">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-307">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4dd64-308">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-308">Select the **Body** tab.</span></span>
* <span data-ttu-id="4dd64-309">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-309">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4dd64-310">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-310">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4dd64-311">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-311">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4dd64-312">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-312">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="4dd64-314">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-314">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4dd64-315">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="4dd64-315">Test the location header URI</span></span>

* <span data-ttu-id="4dd64-316">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-316">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4dd64-317">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="4dd64-317">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4dd64-319">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-319">Set the method to GET.</span></span>
* <span data-ttu-id="4dd64-320">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="4dd64-320">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="4dd64-321">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-321">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4dd64-322">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-322">Add a PutTodoItem method</span></span>

<span data-ttu-id="4dd64-323">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-323">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4dd64-324">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-324">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4dd64-325">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-325">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4dd64-326">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-326">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4dd64-327">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-327">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4dd64-328">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内に項目があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-328">If you get an error calling `PutTodoItem`, call `GET` to ensure there is an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4dd64-329">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="4dd64-329">Test the PutTodoItem method</span></span>

<span data-ttu-id="4dd64-330">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="4dd64-330">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4dd64-331">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-331">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4dd64-332">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-332">Call GET to insure there is an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4dd64-333">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-333">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4dd64-334">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="4dd64-334">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4dd64-336">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-336">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4dd64-337">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-337">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4dd64-338">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-338">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4dd64-339">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="4dd64-339">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4dd64-340">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-340">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4dd64-341">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-341">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4dd64-342">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-342">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="4dd64-343">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-343">Select **Send**</span></span>

<span data-ttu-id="4dd64-344">サンプル アプリではすべてのアイテムを削除できますが、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-344">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="4dd64-345">jQuery での API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="4dd64-345">Call the API with jQuery</span></span>

<span data-ttu-id="4dd64-346">このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-346">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="4dd64-347">jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-347">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="4dd64-348">[静的ファイルを提供し](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-348">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="4dd64-349">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-349">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="4dd64-350">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-350">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4dd64-351">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-351">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4dd64-352">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-352">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4dd64-353">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-353">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4dd64-354">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-354">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4dd64-355">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-355">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4dd64-356">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-356">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4dd64-357">jQuery を取得するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-357">There are several ways to get jQuery.</span></span> <span data-ttu-id="4dd64-358">前のスニペットでは、ライブラリは CDN から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-358">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="4dd64-359">このサンプルでは、API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-359">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="4dd64-360">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-360">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4dd64-361">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="4dd64-361">Get a list of to-do items</span></span>

<span data-ttu-id="4dd64-362">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-362">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4dd64-363">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-363">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4dd64-364">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-364">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4dd64-365">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="4dd64-365">Add a to-do item</span></span>

<span data-ttu-id="4dd64-366">[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-366">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="4dd64-367">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-367">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4dd64-368">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="4dd64-368">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4dd64-369">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-369">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4dd64-370">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="4dd64-370">Update a to-do item</span></span>

<span data-ttu-id="4dd64-371">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="4dd64-371">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4dd64-372">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="4dd64-372">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4dd64-373">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="4dd64-373">Delete a to-do item</span></span>

<span data-ttu-id="4dd64-374">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-374">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="4dd64-375">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4dd64-375">Additional resources</span></span>

<span data-ttu-id="4dd64-376">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="4dd64-376">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4dd64-377">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-377">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4dd64-378">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-378">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4dd64-379">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="4dd64-379">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="4dd64-380">次の手順</span><span class="sxs-lookup"><span data-stu-id="4dd64-380">Next steps</span></span>

<span data-ttu-id="4dd64-381">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="4dd64-381">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4dd64-382">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-382">Create a web api project.</span></span>
> * <span data-ttu-id="4dd64-383">モデル クラスを追加する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-383">Add a model class.</span></span>
> * <span data-ttu-id="4dd64-384">データベース コンテキストを作成する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-384">Create the database context.</span></span>
> * <span data-ttu-id="4dd64-385">データベース コンテキストを登録する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-385">Register the database context.</span></span>
> * <span data-ttu-id="4dd64-386">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-386">Add a controller.</span></span>
> * <span data-ttu-id="4dd64-387">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-387">Add CRUD methods.</span></span>
> * <span data-ttu-id="4dd64-388">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-388">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4dd64-389">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="4dd64-389">Specify return values.</span></span>
> * <span data-ttu-id="4dd64-390">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="4dd64-390">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4dd64-391">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="4dd64-391">Call the web api with jQuery.</span></span>

<span data-ttu-id="4dd64-392">API ヘルプ ページを生成する方法については、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="4dd64-392">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
