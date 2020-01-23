---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 73e547b014d78dcbcbf1c887ebec16e0743d10b9
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294746"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="43269-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="43269-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="43269-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="43269-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="43269-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="43269-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43269-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="43269-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="43269-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="43269-107">Create a web API project.</span></span>
> * <span data-ttu-id="43269-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="43269-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="43269-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="43269-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="43269-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="43269-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="43269-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="43269-111">Call the web API with Postman.</span></span>

<span data-ttu-id="43269-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="43269-113">概要</span><span class="sxs-lookup"><span data-stu-id="43269-113">Overview</span></span>

<span data-ttu-id="43269-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="43269-115">API</span><span class="sxs-lookup"><span data-stu-id="43269-115">API</span></span> | <span data-ttu-id="43269-116">説明</span><span class="sxs-lookup"><span data-stu-id="43269-116">Description</span></span> | <span data-ttu-id="43269-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="43269-117">Request body</span></span> | <span data-ttu-id="43269-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="43269-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="43269-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="43269-119">GET /api/TodoItems</span></span> | <span data-ttu-id="43269-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="43269-120">Get all to-do items</span></span> | <span data-ttu-id="43269-121">None</span><span class="sxs-lookup"><span data-stu-id="43269-121">None</span></span> | <span data-ttu-id="43269-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="43269-122">Array of to-do items</span></span>|
|<span data-ttu-id="43269-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="43269-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="43269-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="43269-124">Get an item by ID</span></span> | <span data-ttu-id="43269-125">None</span><span class="sxs-lookup"><span data-stu-id="43269-125">None</span></span> | <span data-ttu-id="43269-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-126">To-do item</span></span>|
|<span data-ttu-id="43269-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="43269-127">POST /api/TodoItems</span></span> | <span data-ttu-id="43269-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-128">Add a new item</span></span> | <span data-ttu-id="43269-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-129">To-do item</span></span> | <span data-ttu-id="43269-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-130">To-do item</span></span> |
|<span data-ttu-id="43269-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="43269-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="43269-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="43269-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="43269-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-133">To-do item</span></span> | <span data-ttu-id="43269-134">None</span><span class="sxs-lookup"><span data-stu-id="43269-134">None</span></span> |
|<span data-ttu-id="43269-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="43269-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="43269-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="43269-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="43269-137">None</span><span class="sxs-lookup"><span data-stu-id="43269-137">None</span></span> | <span data-ttu-id="43269-138">None</span><span class="sxs-lookup"><span data-stu-id="43269-138">None</span></span>|

<span data-ttu-id="43269-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="43269-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="43269-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="43269-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="43269-149">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="43269-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-151">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="43269-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="43269-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="43269-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="43269-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="43269-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-155">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="43269-158">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="43269-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="43269-159">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="43269-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="43269-160">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="43269-161">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="43269-162">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="43269-162">The preceding commands:</span></span>

  * <span data-ttu-id="43269-163">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="43269-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="43269-164">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="43269-166">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-166">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="43269-168">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="43269-170">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.1]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="43269-171">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="43269-173">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="43269-174">API のテスト</span><span class="sxs-lookup"><span data-stu-id="43269-174">Test the API</span></span>

<span data-ttu-id="43269-175">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="43269-176">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="43269-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43269-178">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="43269-179">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="43269-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="43269-180">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="43269-181">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="43269-183">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="43269-184">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="43269-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="43269-186">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="43269-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="43269-187">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="43269-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="43269-188">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="43269-189">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="43269-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="43269-190">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="43269-191">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="43269-191">Add a model class</span></span>

<span data-ttu-id="43269-192">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="43269-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="43269-193">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="43269-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-195">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="43269-196">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="43269-197">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="43269-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="43269-198">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="43269-199">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="43269-200">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="43269-202">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="43269-203">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="43269-205">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-205">Right-click the project.</span></span> <span data-ttu-id="43269-206">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="43269-207">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="43269-207">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="43269-209">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="43269-210">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="43269-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="43269-212">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="43269-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="43269-213">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="43269-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="43269-214">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="43269-214">Add a database context</span></span>

<span data-ttu-id="43269-215">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="43269-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="43269-216">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="43269-218">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="43269-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="43269-219">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="43269-220">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="43269-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="43269-221">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="43269-222">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="43269-223">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="43269-225">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="43269-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="43269-226">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="43269-227">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="43269-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="43269-229">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="43269-230">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="43269-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="43269-231">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="43269-231">Register the database context</span></span>

<span data-ttu-id="43269-232">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="43269-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="43269-233">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="43269-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="43269-234">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="43269-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="43269-235">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="43269-235">The preceding code:</span></span>

* <span data-ttu-id="43269-236">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="43269-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="43269-237">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="43269-238">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="43269-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="43269-239">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="43269-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-241">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="43269-242">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="43269-243">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="43269-244">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="43269-245">**モデル クラス**で **[TodoItem (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="43269-246">**データ コンテキスト クラス**で **[TodoContext (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="43269-247">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="43269-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="43269-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="43269-249">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="43269-250">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="43269-250">The preceding commands:</span></span>

* <span data-ttu-id="43269-251">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="43269-252">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="43269-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="43269-253">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="43269-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="43269-254">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="43269-254">The generated code:</span></span>

* <span data-ttu-id="43269-255">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="43269-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="43269-256">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="43269-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="43269-257">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="43269-258">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="43269-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="43269-259">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="43269-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="43269-260">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="43269-260">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="43269-261">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-261">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="43269-262">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="43269-262">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="43269-263">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="43269-263">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="43269-264"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="43269-264">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="43269-265">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-265">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="43269-266">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="43269-266">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="43269-267">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="43269-267">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="43269-268">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="43269-268">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="43269-269">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-269">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="43269-270">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-270">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="43269-271">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="43269-271">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="43269-272">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="43269-272">Install Postman</span></span>

<span data-ttu-id="43269-273">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="43269-273">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="43269-274">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="43269-274">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="43269-275">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="43269-275">Start the web app.</span></span>
* <span data-ttu-id="43269-276">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="43269-276">Start Postman.</span></span>
* <span data-ttu-id="43269-277">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-277">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="43269-278">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-278">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="43269-279">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-279">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="43269-280">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="43269-280">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="43269-281">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-281">Create a new request.</span></span>
* <span data-ttu-id="43269-282">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-282">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="43269-283">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-283">Select the **Body** tab.</span></span>
* <span data-ttu-id="43269-284">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-284">Select the **raw** radio button.</span></span>
* <span data-ttu-id="43269-285">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-285">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="43269-286">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="43269-286">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="43269-287">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-287">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="43269-289">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="43269-289">Test the location header URI</span></span>

* <span data-ttu-id="43269-290">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-290">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="43269-291">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="43269-291">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="43269-293">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-293">Set the method to GET.</span></span>
* <span data-ttu-id="43269-294">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="43269-294">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="43269-295">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-295">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="43269-296">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="43269-296">Examine the GET methods</span></span>

<span data-ttu-id="43269-297">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="43269-297">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="43269-298">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="43269-298">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="43269-299">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="43269-299">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="43269-300">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-300">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="43269-301">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="43269-301">Test Get with Postman</span></span>

* <span data-ttu-id="43269-302">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-302">Create a new request.</span></span>
* <span data-ttu-id="43269-303">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-303">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="43269-304">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-304">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="43269-305">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="43269-305">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="43269-306">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-306">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="43269-307">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-307">Select **Send**.</span></span>

<span data-ttu-id="43269-308">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="43269-308">This app uses an in-memory database.</span></span> <span data-ttu-id="43269-309">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="43269-309">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="43269-310">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="43269-310">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="43269-311">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="43269-311">Routing and URL paths</span></span>

<span data-ttu-id="43269-312">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="43269-312">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="43269-313">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-313">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="43269-314">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="43269-314">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="43269-315">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-315">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="43269-316">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="43269-316">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="43269-317">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="43269-317">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="43269-318">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-318">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="43269-319">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="43269-319">This sample doesn't use a template.</span></span> <span data-ttu-id="43269-320">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-320">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="43269-321">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="43269-321">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="43269-322">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="43269-322">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="43269-323">戻り値</span><span class="sxs-lookup"><span data-stu-id="43269-323">Return values</span></span>

<span data-ttu-id="43269-324">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型です](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="43269-324">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="43269-325">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="43269-325">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="43269-326">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="43269-326">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="43269-327">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="43269-327">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="43269-328">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="43269-328">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="43269-329">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="43269-329">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="43269-330">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="43269-330">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="43269-331">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="43269-331">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="43269-332">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-332">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="43269-333">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="43269-333">The PutTodoItem method</span></span>

<span data-ttu-id="43269-334">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="43269-334">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="43269-335">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="43269-335">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="43269-336">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="43269-336">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="43269-337">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="43269-337">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="43269-338">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="43269-338">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="43269-339">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="43269-339">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="43269-340">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="43269-340">Test the PutTodoItem method</span></span>

<span data-ttu-id="43269-341">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="43269-341">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="43269-342">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="43269-342">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="43269-343">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="43269-343">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="43269-344">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-344">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="43269-345">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="43269-345">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="43269-347">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="43269-347">The DeleteTodoItem method</span></span>

<span data-ttu-id="43269-348">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="43269-348">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="43269-349">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="43269-349">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="43269-350">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="43269-350">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="43269-351">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-351">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="43269-352">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-352">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="43269-353">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-353">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="43269-354">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="43269-354">Call the web API with JavaScript</span></span>

<span data-ttu-id="43269-355">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-355">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="43269-356">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="43269-356">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="43269-357">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="43269-357">Create a web API project.</span></span>
> * <span data-ttu-id="43269-358">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="43269-358">Add a model class and a database context.</span></span>
> * <span data-ttu-id="43269-359">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="43269-359">Add a controller.</span></span>
> * <span data-ttu-id="43269-360">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="43269-360">Add CRUD methods.</span></span>
> * <span data-ttu-id="43269-361">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="43269-361">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="43269-362">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="43269-362">Specify return values.</span></span>
> * <span data-ttu-id="43269-363">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="43269-363">Call the web API with Postman.</span></span>
> * <span data-ttu-id="43269-364">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="43269-364">Call the web API with JavaScript.</span></span>

<span data-ttu-id="43269-365">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-365">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="43269-366">概要</span><span class="sxs-lookup"><span data-stu-id="43269-366">Overview</span></span>

<span data-ttu-id="43269-367">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-367">This tutorial creates the following API:</span></span>

|<span data-ttu-id="43269-368">API</span><span class="sxs-lookup"><span data-stu-id="43269-368">API</span></span> | <span data-ttu-id="43269-369">説明</span><span class="sxs-lookup"><span data-stu-id="43269-369">Description</span></span> | <span data-ttu-id="43269-370">要求本文</span><span class="sxs-lookup"><span data-stu-id="43269-370">Request body</span></span> | <span data-ttu-id="43269-371">応答本文</span><span class="sxs-lookup"><span data-stu-id="43269-371">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="43269-372">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="43269-372">GET /api/TodoItems</span></span> | <span data-ttu-id="43269-373">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="43269-373">Get all to-do items</span></span> | <span data-ttu-id="43269-374">None</span><span class="sxs-lookup"><span data-stu-id="43269-374">None</span></span> | <span data-ttu-id="43269-375">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="43269-375">Array of to-do items</span></span>|
|<span data-ttu-id="43269-376">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="43269-376">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="43269-377">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="43269-377">Get an item by ID</span></span> | <span data-ttu-id="43269-378">None</span><span class="sxs-lookup"><span data-stu-id="43269-378">None</span></span> | <span data-ttu-id="43269-379">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-379">To-do item</span></span>|
|<span data-ttu-id="43269-380">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="43269-380">POST /api/TodoItems</span></span> | <span data-ttu-id="43269-381">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-381">Add a new item</span></span> | <span data-ttu-id="43269-382">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-382">To-do item</span></span> | <span data-ttu-id="43269-383">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-383">To-do item</span></span> |
|<span data-ttu-id="43269-384">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="43269-384">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="43269-385">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="43269-385">Update an existing item &nbsp;</span></span> | <span data-ttu-id="43269-386">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="43269-386">To-do item</span></span> | <span data-ttu-id="43269-387">None</span><span class="sxs-lookup"><span data-stu-id="43269-387">None</span></span> |
|<span data-ttu-id="43269-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="43269-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="43269-389">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="43269-389">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="43269-390">None</span><span class="sxs-lookup"><span data-stu-id="43269-390">None</span></span> | <span data-ttu-id="43269-391">None</span><span class="sxs-lookup"><span data-stu-id="43269-391">None</span></span>|

<span data-ttu-id="43269-392">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="43269-392">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="43269-398">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="43269-398">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-399">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-400">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-400">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-401">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-401">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="43269-402">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="43269-402">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-403">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-404">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-404">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="43269-405">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-405">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="43269-406">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-406">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="43269-407">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="43269-407">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="43269-408">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-408">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="43269-409">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="43269-409">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="43269-412">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="43269-412">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="43269-413">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="43269-413">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="43269-414">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-414">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="43269-415">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="43269-415">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="43269-416">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-416">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-417">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-417">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="43269-418">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-418">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="43269-420">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-420">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="43269-422">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="43269-422">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="43269-423">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-423">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="43269-425">API のテスト</span><span class="sxs-lookup"><span data-stu-id="43269-425">Test the API</span></span>

<span data-ttu-id="43269-426">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-426">The project template creates a `values` API.</span></span> <span data-ttu-id="43269-427">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="43269-427">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-428">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43269-429">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-429">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="43269-430">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="43269-430">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="43269-431">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-431">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="43269-432">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-432">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="43269-434">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="43269-434">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="43269-435">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="43269-435">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="43269-437">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="43269-437">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="43269-438">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="43269-438">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="43269-439">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-439">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="43269-440">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="43269-440">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="43269-441">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-441">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="43269-442">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="43269-442">Add a model class</span></span>

<span data-ttu-id="43269-443">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="43269-443">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="43269-444">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="43269-444">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-445">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-445">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-446">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-446">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="43269-447">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-447">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="43269-448">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="43269-448">Name the folder *Models*.</span></span>

* <span data-ttu-id="43269-449">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-449">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="43269-450">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-450">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="43269-451">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-451">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43269-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43269-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="43269-453">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-453">Add a folder named *Models*.</span></span>

* <span data-ttu-id="43269-454">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-454">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43269-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="43269-456">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-456">Right-click the project.</span></span> <span data-ttu-id="43269-457">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-457">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="43269-458">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="43269-458">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="43269-460">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-460">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="43269-461">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-461">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="43269-462">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-462">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="43269-463">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="43269-463">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="43269-464">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="43269-464">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="43269-465">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="43269-465">Add a database context</span></span>

<span data-ttu-id="43269-466">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="43269-466">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="43269-467">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-467">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-469">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-469">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="43269-470">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-470">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="43269-471">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-471">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="43269-472">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-472">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="43269-473">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-473">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="43269-474">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="43269-474">Register the database context</span></span>

<span data-ttu-id="43269-475">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="43269-475">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="43269-476">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="43269-476">The container provides the service to controllers.</span></span>

<span data-ttu-id="43269-477">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="43269-477">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="43269-478">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="43269-478">The preceding code:</span></span>

* <span data-ttu-id="43269-479">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="43269-479">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="43269-480">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-480">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="43269-481">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="43269-481">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="43269-482">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="43269-482">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-484">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="43269-484">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="43269-485">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-485">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="43269-486">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-486">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="43269-487">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-487">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="43269-489">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-489">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="43269-490">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-490">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="43269-491">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-491">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="43269-492">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="43269-492">The preceding code:</span></span>

* <span data-ttu-id="43269-493">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="43269-493">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="43269-494">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="43269-494">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="43269-495">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="43269-495">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="43269-496">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-496">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="43269-497">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="43269-497">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="43269-498">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="43269-498">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="43269-499">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="43269-499">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="43269-500">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="43269-500">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="43269-501">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-501">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="43269-502">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="43269-502">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="43269-503">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="43269-503">Add Get methods</span></span>

<span data-ttu-id="43269-504">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-504">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="43269-505">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="43269-505">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="43269-506">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="43269-506">Stop the app if it's still running.</span></span> <span data-ttu-id="43269-507">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="43269-507">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="43269-508">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="43269-508">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="43269-509">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="43269-509">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="43269-510">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-510">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="43269-511">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="43269-511">Routing and URL paths</span></span>

<span data-ttu-id="43269-512">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="43269-512">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="43269-513">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-513">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="43269-514">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="43269-514">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="43269-515">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-515">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="43269-516">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="43269-516">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="43269-517">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="43269-517">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="43269-518">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-518">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="43269-519">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="43269-519">This sample doesn't use a template.</span></span> <span data-ttu-id="43269-520">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-520">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="43269-521">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="43269-521">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="43269-522">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="43269-522">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="43269-523">戻り値</span><span class="sxs-lookup"><span data-stu-id="43269-523">Return values</span></span>

<span data-ttu-id="43269-524">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型です](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="43269-524">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="43269-525">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="43269-525">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="43269-526">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="43269-526">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="43269-527">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="43269-527">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="43269-528">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="43269-528">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="43269-529">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="43269-529">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="43269-530">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="43269-530">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="43269-531">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="43269-531">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="43269-532">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-532">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="43269-533">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="43269-533">Test the GetTodoItems method</span></span>

<span data-ttu-id="43269-534">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="43269-534">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="43269-535">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="43269-535">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="43269-536">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="43269-536">Start the web app.</span></span>
* <span data-ttu-id="43269-537">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="43269-537">Start Postman.</span></span>
* <span data-ttu-id="43269-538">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-538">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43269-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43269-539">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43269-540">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-540">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="43269-541">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43269-541">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="43269-542">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-542">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="43269-543">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-543">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="43269-544">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="43269-544">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="43269-545">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-545">Create a new request.</span></span>
  * <span data-ttu-id="43269-546">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-546">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="43269-547">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-547">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="43269-548">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="43269-548">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="43269-549">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-549">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="43269-550">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-550">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="43269-552">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="43269-552">Add a Create method</span></span>

<span data-ttu-id="43269-553">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-553">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="43269-554">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="43269-554">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="43269-555">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="43269-555">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="43269-556">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="43269-556">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="43269-557">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-557">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="43269-558">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="43269-558">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="43269-559">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-559">Adds a `Location` header to the response.</span></span> <span data-ttu-id="43269-560">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="43269-560">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="43269-561">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-561">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="43269-562">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-562">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="43269-563">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="43269-563">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="43269-564">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="43269-564">Test the PostTodoItem method</span></span>

* <span data-ttu-id="43269-565">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="43269-565">Build the project.</span></span>
* <span data-ttu-id="43269-566">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-566">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="43269-567">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-567">Select the **Body** tab.</span></span>
* <span data-ttu-id="43269-568">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-568">Select the **raw** radio button.</span></span>
* <span data-ttu-id="43269-569">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-569">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="43269-570">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="43269-570">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="43269-571">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-571">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="43269-573">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="43269-573">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="43269-574">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="43269-574">Test the location header URI</span></span>

* <span data-ttu-id="43269-575">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-575">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="43269-576">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="43269-576">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="43269-578">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-578">Set the method to GET.</span></span>
* <span data-ttu-id="43269-579">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="43269-579">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="43269-580">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-580">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="43269-581">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="43269-581">Add a PutTodoItem method</span></span>

<span data-ttu-id="43269-582">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-582">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="43269-583">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="43269-583">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="43269-584">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="43269-584">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="43269-585">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="43269-585">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="43269-586">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="43269-586">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="43269-587">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="43269-587">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="43269-588">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="43269-588">Test the PutTodoItem method</span></span>

<span data-ttu-id="43269-589">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="43269-589">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="43269-590">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="43269-590">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="43269-591">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="43269-591">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="43269-592">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-592">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="43269-593">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="43269-593">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="43269-595">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="43269-595">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="43269-596">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-596">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="43269-597">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="43269-597">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="43269-598">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="43269-598">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="43269-599">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="43269-599">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="43269-600">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-600">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="43269-601">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="43269-601">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="43269-602">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="43269-602">Select **Send**.</span></span>

<span data-ttu-id="43269-603">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="43269-603">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="43269-604">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="43269-604">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="43269-605">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="43269-605">Call the web API with JavaScript</span></span>

<span data-ttu-id="43269-606">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-606">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="43269-607">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="43269-607">jQuery initiates the request.</span></span> <span data-ttu-id="43269-608">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="43269-608">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="43269-609">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="43269-609">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="43269-610">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="43269-610">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="43269-611">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-611">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="43269-612">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-612">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="43269-613">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="43269-613">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="43269-614">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="43269-614">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="43269-615">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="43269-615">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="43269-616">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="43269-616">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="43269-617">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="43269-617">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="43269-618">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="43269-618">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="43269-619">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="43269-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="43269-620">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="43269-620">Get a list of to-do items</span></span>

<span data-ttu-id="43269-621">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="43269-621">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="43269-622">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="43269-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="43269-623">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="43269-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="43269-624">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="43269-624">Add a to-do item</span></span>

<span data-ttu-id="43269-625">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="43269-625">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="43269-626">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="43269-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="43269-627">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="43269-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="43269-628">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="43269-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="43269-629">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="43269-629">Update a to-do item</span></span>

<span data-ttu-id="43269-630">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="43269-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="43269-631">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="43269-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="43269-632">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="43269-632">Delete a to-do item</span></span>

<span data-ttu-id="43269-633">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="43269-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="43269-634">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="43269-634">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="43269-635">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="43269-635">Additional resources</span></span>

<span data-ttu-id="43269-636">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="43269-636">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="43269-637">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-637">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="43269-638">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="43269-638">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="43269-639">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="43269-639">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
