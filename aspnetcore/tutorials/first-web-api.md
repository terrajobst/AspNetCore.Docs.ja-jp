---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2019
uid: tutorials/first-web-api
ms.openlocfilehash: a53f7019c1079296f073e743ddbf9d90fc5abad3
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555883"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="7f735-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="7f735-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="7f735-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7f735-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7f735-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="7f735-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="7f735-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7f735-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7f735-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="7f735-107">Create a web API project.</span></span>
> * <span data-ttu-id="7f735-108">モデル クラスを追加する。</span><span class="sxs-lookup"><span data-stu-id="7f735-108">Add a model class.</span></span>
> * <span data-ttu-id="7f735-109">データベース コンテキストを作成する。</span><span class="sxs-lookup"><span data-stu-id="7f735-109">Create the database context.</span></span>
> * <span data-ttu-id="7f735-110">データベース コンテキストを登録する。</span><span class="sxs-lookup"><span data-stu-id="7f735-110">Register the database context.</span></span>
> * <span data-ttu-id="7f735-111">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="7f735-111">Add a controller.</span></span>
> * <span data-ttu-id="7f735-112">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="7f735-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="7f735-113">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="7f735-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="7f735-114">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="7f735-114">Specify return values.</span></span>
> * <span data-ttu-id="7f735-115">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="7f735-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="7f735-116">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="7f735-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="7f735-117">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="7f735-118">概要</span><span class="sxs-lookup"><span data-stu-id="7f735-118">Overview</span></span>

<span data-ttu-id="7f735-119">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7f735-120">API</span><span class="sxs-lookup"><span data-stu-id="7f735-120">API</span></span> | <span data-ttu-id="7f735-121">説明</span><span class="sxs-lookup"><span data-stu-id="7f735-121">Description</span></span> | <span data-ttu-id="7f735-122">要求本文</span><span class="sxs-lookup"><span data-stu-id="7f735-122">Request body</span></span> | <span data-ttu-id="7f735-123">応答本文</span><span class="sxs-lookup"><span data-stu-id="7f735-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="7f735-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="7f735-124">GET /api/todo</span></span> | <span data-ttu-id="7f735-125">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="7f735-125">Get all to-do items</span></span> | <span data-ttu-id="7f735-126">なし</span><span class="sxs-lookup"><span data-stu-id="7f735-126">None</span></span> | <span data-ttu-id="7f735-127">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="7f735-127">Array of to-do items</span></span>|
|<span data-ttu-id="7f735-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="7f735-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="7f735-129">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="7f735-129">Get an item by ID</span></span> | <span data-ttu-id="7f735-130">なし</span><span class="sxs-lookup"><span data-stu-id="7f735-130">None</span></span> | <span data-ttu-id="7f735-131">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="7f735-131">To-do item</span></span>|
|<span data-ttu-id="7f735-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="7f735-132">POST /api/todo</span></span> | <span data-ttu-id="7f735-133">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-133">Add a new item</span></span> | <span data-ttu-id="7f735-134">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="7f735-134">To-do item</span></span> | <span data-ttu-id="7f735-135">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="7f735-135">To-do item</span></span> |
|<span data-ttu-id="7f735-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="7f735-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="7f735-137">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f735-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7f735-138">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="7f735-138">To-do item</span></span> | <span data-ttu-id="7f735-139">なし</span><span class="sxs-lookup"><span data-stu-id="7f735-139">None</span></span> |
|<span data-ttu-id="7f735-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f735-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="7f735-141">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f735-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7f735-142">なし</span><span class="sxs-lookup"><span data-stu-id="7f735-142">None</span></span> | <span data-ttu-id="7f735-143">なし</span><span class="sxs-lookup"><span data-stu-id="7f735-143">None</span></span>|

<span data-ttu-id="7f735-144">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="7f735-144">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7f735-150">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7f735-150">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f735-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f735-151">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f735-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f735-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f735-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f735-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7f735-154">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="7f735-154">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f735-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f735-155">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f735-156">**[ファイル]** メニューで、 **[新規作成]** 、 >  **[プロジェクト]** の順に作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-156">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7f735-157">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-157">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7f735-158">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-158">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7f735-159">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7f735-159">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="7f735-160">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-160">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="7f735-161">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="7f735-161">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f735-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f735-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f735-164">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="7f735-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7f735-165">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="7f735-165">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7f735-166">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7f735-166">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="7f735-167">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="7f735-167">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="7f735-168">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-168">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f735-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f735-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f735-170">**[ファイル]** 、 **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-170">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7f735-172">**[.NET Core アプリ]** 、 >  **[ASP.NET Core Web API]** 、 >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-172">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7f735-174">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="7f735-174">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="7f735-175">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-175">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="7f735-177">API のテスト</span><span class="sxs-lookup"><span data-stu-id="7f735-177">Test the API</span></span>

<span data-ttu-id="7f735-178">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-178">The project template creates a `values` API.</span></span> <span data-ttu-id="7f735-179">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="7f735-179">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f735-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f735-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f735-181">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7f735-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7f735-182">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="7f735-182">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7f735-183">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-183">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7f735-184">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-184">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f735-185">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f735-185">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f735-186">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7f735-186">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7f735-187">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="7f735-187">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f735-188">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f735-188">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f735-189">**[実行]** 、 **[デバッグありで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="7f735-189">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="7f735-190">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="7f735-190">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7f735-191">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-191">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7f735-192">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="7f735-192">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="7f735-193">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-193">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7f735-194">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-194">Add a model class</span></span>

<span data-ttu-id="7f735-195">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="7f735-195">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7f735-196">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="7f735-196">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f735-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f735-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f735-198">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-198">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7f735-199">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-199">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f735-200">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="7f735-200">Name the folder *Models*.</span></span>

* <span data-ttu-id="7f735-201">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-201">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7f735-202">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-202">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7f735-203">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-203">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f735-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f735-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f735-205">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-205">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7f735-206">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-206">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f735-207">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f735-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f735-208">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-208">Right-click the project.</span></span> <span data-ttu-id="7f735-209">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-209">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f735-210">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="7f735-210">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7f735-212">*Models* フォルダーを右クリックし、 **[追加]** 、 **[新しいファイル]** 、 **[全般]** 、 **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-212">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7f735-213">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-213">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7f735-214">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-214">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7f735-215">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="7f735-215">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7f735-216">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-216">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7f735-217">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-217">Add a database context</span></span>

<span data-ttu-id="7f735-218">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="7f735-218">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7f735-219">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-219">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f735-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f735-220">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f735-221">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-221">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7f735-222">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-222">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7f735-223">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f735-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7f735-224">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-224">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7f735-225">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-225">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7f735-226">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="7f735-226">Register the database context</span></span>

<span data-ttu-id="7f735-227">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-227">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7f735-228">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7f735-228">The container provides the service to controllers.</span></span>

<span data-ttu-id="7f735-229">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7f735-229">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="7f735-230">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="7f735-230">The preceding code:</span></span>

* <span data-ttu-id="7f735-231">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="7f735-231">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7f735-232">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-232">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7f735-233">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-233">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7f735-234">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-234">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f735-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f735-235">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f735-236">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7f735-236">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7f735-237">**[追加]**  >  **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-237">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="7f735-238">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-238">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="7f735-239">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-239">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7f735-241">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f735-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7f735-242">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-242">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="7f735-243">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-243">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="7f735-244">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="7f735-244">The preceding code:</span></span>

* <span data-ttu-id="7f735-245">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="7f735-245">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="7f735-246">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="7f735-246">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7f735-247">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="7f735-247">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7f735-248">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f735-248">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7f735-249">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="7f735-249">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7f735-250">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-250">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="7f735-251">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="7f735-251">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="7f735-252">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-252">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="7f735-253">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-253">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="7f735-254">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-254">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="7f735-255">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-255">Add Get methods</span></span>

<span data-ttu-id="7f735-256">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-256">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="7f735-257">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="7f735-257">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="7f735-258">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="7f735-258">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="7f735-259">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7f735-259">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="7f735-260">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-260">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="7f735-261">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="7f735-261">Routing and URL paths</span></span>

<span data-ttu-id="7f735-262">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="7f735-262">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7f735-263">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-263">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7f735-264">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="7f735-264">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="7f735-265">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-265">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7f735-266">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="7f735-266">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="7f735-267">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="7f735-267">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7f735-268">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-268">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7f735-269">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="7f735-269">This sample doesn't use a template.</span></span> <span data-ttu-id="7f735-270">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f735-270">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7f735-271">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="7f735-271">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7f735-272">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-272">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7f735-273">戻り値</span><span class="sxs-lookup"><span data-stu-id="7f735-273">Return values</span></span>

<span data-ttu-id="7f735-274">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="7f735-274">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7f735-275">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="7f735-275">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7f735-276">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-276">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7f735-277">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-277">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7f735-278">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="7f735-278">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7f735-279">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-279">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7f735-280">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="7f735-280">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7f735-281">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="7f735-281">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7f735-282">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-282">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="7f735-283">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="7f735-283">Test the GetTodoItems method</span></span>

<span data-ttu-id="7f735-284">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="7f735-284">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7f735-285">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7f735-285">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="7f735-286">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="7f735-286">Start the web app.</span></span>
* <span data-ttu-id="7f735-287">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="7f735-287">Start Postman.</span></span>
* <span data-ttu-id="7f735-288">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="7f735-288">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="7f735-289">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="7f735-289">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="7f735-290">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="7f735-290">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="7f735-291">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-291">Create a new request.</span></span>
  * <span data-ttu-id="7f735-292">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-292">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="7f735-293">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-293">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="7f735-294">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="7f735-294">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="7f735-295">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-295">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7f735-296">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-296">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="7f735-298">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-298">Add a Create method</span></span>

<span data-ttu-id="7f735-299">次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-299">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7f735-300">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="7f735-300">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7f735-301">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="7f735-301">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7f735-302">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="7f735-302">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="7f735-303">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-303">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="7f735-304">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="7f735-304">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7f735-305">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-305">Adds a `Location` header to the response.</span></span> <span data-ttu-id="7f735-306">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-306">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7f735-307">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f735-307">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7f735-308">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-308">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7f735-309">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="7f735-309">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="7f735-310">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="7f735-310">Test the PostTodoItem method</span></span>

* <span data-ttu-id="7f735-311">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="7f735-311">Build the project.</span></span>
* <span data-ttu-id="7f735-312">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-312">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7f735-313">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-313">Select the **Body** tab.</span></span>
* <span data-ttu-id="7f735-314">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-314">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7f735-315">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-315">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7f735-316">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="7f735-316">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7f735-317">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-317">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="7f735-319">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-319">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7f735-320">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="7f735-320">Test the location header URI</span></span>

* <span data-ttu-id="7f735-321">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-321">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7f735-322">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="7f735-322">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="7f735-324">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-324">Set the method to GET.</span></span>
* <span data-ttu-id="7f735-325">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="7f735-325">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="7f735-326">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-326">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="7f735-327">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-327">Add a PutTodoItem method</span></span>

<span data-ttu-id="7f735-328">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-328">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="7f735-329">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="7f735-329">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7f735-330">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="7f735-330">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7f735-331">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="7f735-331">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7f735-332">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="7f735-332">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7f735-333">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="7f735-333">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7f735-334">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="7f735-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="7f735-335">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="7f735-335">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="7f735-336">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-336">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7f735-337">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7f735-337">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7f735-338">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-338">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7f735-339">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="7f735-339">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="7f735-341">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-341">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="7f735-342">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-342">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="7f735-343">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="7f735-343">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7f735-344">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="7f735-344">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7f735-345">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="7f735-345">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7f735-346">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-346">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7f735-347">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-347">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="7f735-348">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f735-348">Select **Send**</span></span>

<span data-ttu-id="7f735-349">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="7f735-349">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="7f735-350">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-350">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="7f735-351">jQuery での API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="7f735-351">Call the API with jQuery</span></span>

<span data-ttu-id="7f735-352">このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-352">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="7f735-353">jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。</span><span class="sxs-lookup"><span data-stu-id="7f735-353">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="7f735-354">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-354">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="7f735-355">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7f735-355">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="7f735-356">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-356">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="7f735-357">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-357">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="7f735-358">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="7f735-358">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="7f735-359">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f735-359">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="7f735-360">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-360">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="7f735-361">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="7f735-361">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="7f735-362">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="7f735-362">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="7f735-363">jQuery を取得するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="7f735-363">There are several ways to get jQuery.</span></span> <span data-ttu-id="7f735-364">前のスニペットでは、ライブラリは CDN から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7f735-364">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="7f735-365">このサンプルでは、API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7f735-365">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="7f735-366">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="7f735-366">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="7f735-367">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="7f735-367">Get a list of to-do items</span></span>

<span data-ttu-id="7f735-368">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。</span><span class="sxs-lookup"><span data-stu-id="7f735-368">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="7f735-369">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-369">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="7f735-370">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-370">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="7f735-371">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="7f735-371">Add a to-do item</span></span>

<span data-ttu-id="7f735-372">[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="7f735-372">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="7f735-373">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-373">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="7f735-374">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="7f735-374">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="7f735-375">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="7f735-375">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="7f735-376">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="7f735-376">Update a to-do item</span></span>

<span data-ttu-id="7f735-377">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="7f735-377">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="7f735-378">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="7f735-378">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="7f735-379">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="7f735-379">Delete a to-do item</span></span>

<span data-ttu-id="7f735-380">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="7f735-380">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="7f735-381">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7f735-381">Additional resources</span></span>

<span data-ttu-id="7f735-382">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="7f735-382">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="7f735-383">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f735-383">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7f735-384">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f735-384">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="7f735-385">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="7f735-385">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="7f735-386">次の手順</span><span class="sxs-lookup"><span data-stu-id="7f735-386">Next steps</span></span>

<span data-ttu-id="7f735-387">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="7f735-387">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7f735-388">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="7f735-388">Create a web api project.</span></span>
> * <span data-ttu-id="7f735-389">モデル クラスを追加する。</span><span class="sxs-lookup"><span data-stu-id="7f735-389">Add a model class.</span></span>
> * <span data-ttu-id="7f735-390">データベース コンテキストを作成する。</span><span class="sxs-lookup"><span data-stu-id="7f735-390">Create the database context.</span></span>
> * <span data-ttu-id="7f735-391">データベース コンテキストを登録する。</span><span class="sxs-lookup"><span data-stu-id="7f735-391">Register the database context.</span></span>
> * <span data-ttu-id="7f735-392">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="7f735-392">Add a controller.</span></span>
> * <span data-ttu-id="7f735-393">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="7f735-393">Add CRUD methods.</span></span>
> * <span data-ttu-id="7f735-394">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="7f735-394">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="7f735-395">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="7f735-395">Specify return values.</span></span>
> * <span data-ttu-id="7f735-396">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="7f735-396">Call the web API with Postman.</span></span>
> * <span data-ttu-id="7f735-397">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="7f735-397">Call the web api with jQuery.</span></span>

<span data-ttu-id="7f735-398">API ヘルプ ページを生成する方法については、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="7f735-398">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
