---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 25bfccb136d875b454034bd011828c9f3b6cd3d8
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113285"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="250b7-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="250b7-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="250b7-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="250b7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="250b7-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="250b7-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="250b7-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="250b7-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="250b7-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="250b7-107">Create a web API project.</span></span>
> * <span data-ttu-id="250b7-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="250b7-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="250b7-109">CRUD メソッドを使用してコントローラーをスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="250b7-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="250b7-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="250b7-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="250b7-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="250b7-111">Call the web API with Postman.</span></span>

<span data-ttu-id="250b7-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="250b7-113">概要</span><span class="sxs-lookup"><span data-stu-id="250b7-113">Overview</span></span>

<span data-ttu-id="250b7-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="250b7-115">API</span><span class="sxs-lookup"><span data-stu-id="250b7-115">API</span></span> | <span data-ttu-id="250b7-116">説明</span><span class="sxs-lookup"><span data-stu-id="250b7-116">Description</span></span> | <span data-ttu-id="250b7-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="250b7-117">Request body</span></span> | <span data-ttu-id="250b7-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="250b7-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="250b7-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="250b7-119">GET /api/TodoItems</span></span> | <span data-ttu-id="250b7-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="250b7-120">Get all to-do items</span></span> | <span data-ttu-id="250b7-121">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-121">None</span></span> | <span data-ttu-id="250b7-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="250b7-122">Array of to-do items</span></span>|
|<span data-ttu-id="250b7-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="250b7-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="250b7-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="250b7-124">Get an item by ID</span></span> | <span data-ttu-id="250b7-125">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-125">None</span></span> | <span data-ttu-id="250b7-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-126">To-do item</span></span>|
|<span data-ttu-id="250b7-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="250b7-127">POST /api/TodoItems</span></span> | <span data-ttu-id="250b7-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-128">Add a new item</span></span> | <span data-ttu-id="250b7-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-129">To-do item</span></span> | <span data-ttu-id="250b7-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-130">To-do item</span></span> |
|<span data-ttu-id="250b7-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="250b7-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="250b7-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="250b7-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="250b7-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-133">To-do item</span></span> | <span data-ttu-id="250b7-134">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-134">None</span></span> |
|<span data-ttu-id="250b7-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="250b7-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="250b7-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="250b7-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="250b7-137">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-137">None</span></span> | <span data-ttu-id="250b7-138">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-138">None</span></span>|

<span data-ttu-id="250b7-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="250b7-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="250b7-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="250b7-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="250b7-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="250b7-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-151">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="250b7-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="250b7-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="250b7-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="250b7-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="250b7-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="250b7-156">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="250b7-156">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="250b7-159">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="250b7-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="250b7-160">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="250b7-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="250b7-161">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="250b7-162">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="250b7-163">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="250b7-163">The preceding commands:</span></span>

  * <span data-ttu-id="250b7-164">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="250b7-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="250b7-165">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="250b7-167">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-167">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="250b7-169">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="250b7-171">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.0]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="250b7-172">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="250b7-174">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```console
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="250b7-175">API のテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-175">Test the API</span></span>

<span data-ttu-id="250b7-176">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="250b7-177">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="250b7-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="250b7-179">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="250b7-180">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="250b7-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="250b7-181">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="250b7-182">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="250b7-184">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="250b7-185">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="250b7-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="250b7-187">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="250b7-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="250b7-188">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="250b7-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="250b7-189">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="250b7-190">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="250b7-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="250b7-191">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-191">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="250b7-192">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-192">Add a model class</span></span>

<span data-ttu-id="250b7-193">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="250b7-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="250b7-194">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="250b7-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-196">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="250b7-197">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="250b7-198">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="250b7-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="250b7-199">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="250b7-200">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="250b7-201">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="250b7-203">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="250b7-204">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="250b7-206">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-206">Right-click the project.</span></span> <span data-ttu-id="250b7-207">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="250b7-208">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="250b7-208">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="250b7-210">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="250b7-211">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="250b7-212">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="250b7-213">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="250b7-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="250b7-214">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="250b7-215">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-215">Add a database context</span></span>

<span data-ttu-id="250b7-216">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="250b7-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="250b7-217">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="250b7-219">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="250b7-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="250b7-220">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="250b7-221">**[プレリリースを含める]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="250b7-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="250b7-222">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="250b7-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="250b7-223">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="250b7-224">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="250b7-225">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="250b7-227">TodoContext データベースコンテキストを追加する</span><span class="sxs-lookup"><span data-stu-id="250b7-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="250b7-228">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="250b7-229">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="250b7-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="250b7-231">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="250b7-232">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="250b7-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="250b7-233">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="250b7-233">Register the database context</span></span>

<span data-ttu-id="250b7-234">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="250b7-235">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="250b7-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="250b7-236">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="250b7-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="250b7-237">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="250b7-237">The preceding code:</span></span>

* <span data-ttu-id="250b7-238">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="250b7-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="250b7-239">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="250b7-240">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="250b7-241">コントローラーをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="250b7-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-243">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="250b7-244">**[追加]** > **[新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="250b7-245">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="250b7-246">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="250b7-247">**モデル クラス**で **[TodoItem (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="250b7-248">**データ コンテキスト クラス**で **[TodoContext (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="250b7-249">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="250b7-250">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="250b7-251">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-251">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="250b7-252">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="250b7-252">The preceding commands:</span></span>

* <span data-ttu-id="250b7-253">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="250b7-254">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="250b7-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="250b7-255">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="250b7-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="250b7-256">生成されたコード:</span><span class="sxs-lookup"><span data-stu-id="250b7-256">The generated code:</span></span>

* <span data-ttu-id="250b7-257">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="250b7-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="250b7-258">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="250b7-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="250b7-259">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="250b7-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="250b7-260">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="250b7-261">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="250b7-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="250b7-262">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="250b7-263">PostTodoItem create 作成メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="250b7-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="250b7-264">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="250b7-265">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="250b7-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="250b7-266">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="250b7-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="250b7-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="250b7-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="250b7-268">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="250b7-269">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="250b7-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="250b7-270">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="250b7-271">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="250b7-272">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="250b7-273">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="250b7-274">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="250b7-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="250b7-275">Postman をインストールする</span><span class="sxs-lookup"><span data-stu-id="250b7-275">Install Postman</span></span>

<span data-ttu-id="250b7-276">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="250b7-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="250b7-277">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="250b7-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="250b7-278">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="250b7-278">Start the web app.</span></span>
* <span data-ttu-id="250b7-279">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="250b7-279">Start Postman.</span></span>
* <span data-ttu-id="250b7-280">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="250b7-281">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="250b7-282">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="250b7-283">Postman を使用して PostTodoItem をテストする</span><span class="sxs-lookup"><span data-stu-id="250b7-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="250b7-284">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-284">Create a new request.</span></span>
* <span data-ttu-id="250b7-285">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="250b7-286">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="250b7-287">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="250b7-288">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="250b7-289">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="250b7-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="250b7-290">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-290">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="250b7-292">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-292">Test the location header URI</span></span>

* <span data-ttu-id="250b7-293">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="250b7-294">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="250b7-294">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="250b7-296">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-296">Set the method to GET.</span></span>
* <span data-ttu-id="250b7-297">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="250b7-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="250b7-298">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="250b7-299">GET メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="250b7-299">Examine the GET methods</span></span>

<span data-ttu-id="250b7-300">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="250b7-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="250b7-301">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="250b7-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="250b7-302">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="250b7-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="250b7-303">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="250b7-304">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="250b7-304">Test Get with Postman</span></span>

* <span data-ttu-id="250b7-305">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-305">Create a new request.</span></span>
* <span data-ttu-id="250b7-306">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="250b7-307">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="250b7-308">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="250b7-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="250b7-309">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="250b7-310">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-310">Select **Send**.</span></span>

<span data-ttu-id="250b7-311">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-311">This app uses an in-memory database.</span></span> <span data-ttu-id="250b7-312">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="250b7-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="250b7-313">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="250b7-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="250b7-314">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="250b7-314">Routing and URL paths</span></span>

<span data-ttu-id="250b7-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="250b7-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="250b7-316">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="250b7-317">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="250b7-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="250b7-318">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="250b7-319">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="250b7-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="250b7-320">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="250b7-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="250b7-321">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="250b7-322">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="250b7-322">This sample doesn't use a template.</span></span> <span data-ttu-id="250b7-323">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="250b7-324">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="250b7-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="250b7-325">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="250b7-326">戻り値</span><span class="sxs-lookup"><span data-stu-id="250b7-326">Return values</span></span>

<span data-ttu-id="250b7-327">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="250b7-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="250b7-328">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="250b7-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="250b7-329">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="250b7-330">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="250b7-331">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="250b7-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="250b7-332">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="250b7-333">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="250b7-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="250b7-334">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="250b7-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="250b7-335">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="250b7-336">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="250b7-336">The PutTodoItem method</span></span>

<span data-ttu-id="250b7-337">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="250b7-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="250b7-338">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="250b7-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="250b7-339">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="250b7-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="250b7-340">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="250b7-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="250b7-341">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="250b7-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="250b7-342">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="250b7-343">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="250b7-344">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="250b7-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="250b7-345">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="250b7-346">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="250b7-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="250b7-347">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="250b7-348">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="250b7-348">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="250b7-350">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="250b7-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="250b7-351">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="250b7-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="250b7-352">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="250b7-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="250b7-353">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="250b7-354">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="250b7-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="250b7-355">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="250b7-356">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="250b7-357">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-357">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="250b7-358">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="250b7-358">Call the web API with JavaScript</span></span>

<span data-ttu-id="250b7-359">手順については、「[チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-359">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="250b7-360">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="250b7-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="250b7-361">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="250b7-361">Create a web API project.</span></span>
> * <span data-ttu-id="250b7-362">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="250b7-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="250b7-363">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="250b7-363">Add a controller.</span></span>
> * <span data-ttu-id="250b7-364">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="250b7-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="250b7-365">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="250b7-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="250b7-366">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="250b7-366">Specify return values.</span></span>
> * <span data-ttu-id="250b7-367">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="250b7-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="250b7-368">JavaScript で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="250b7-368">Call the web API with JavaScript.</span></span>

<span data-ttu-id="250b7-369">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="250b7-370">概要</span><span class="sxs-lookup"><span data-stu-id="250b7-370">Overview</span></span>

<span data-ttu-id="250b7-371">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="250b7-372">API</span><span class="sxs-lookup"><span data-stu-id="250b7-372">API</span></span> | <span data-ttu-id="250b7-373">説明</span><span class="sxs-lookup"><span data-stu-id="250b7-373">Description</span></span> | <span data-ttu-id="250b7-374">要求本文</span><span class="sxs-lookup"><span data-stu-id="250b7-374">Request body</span></span> | <span data-ttu-id="250b7-375">応答本文</span><span class="sxs-lookup"><span data-stu-id="250b7-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="250b7-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="250b7-376">GET /api/TodoItems</span></span> | <span data-ttu-id="250b7-377">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="250b7-377">Get all to-do items</span></span> | <span data-ttu-id="250b7-378">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-378">None</span></span> | <span data-ttu-id="250b7-379">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="250b7-379">Array of to-do items</span></span>|
|<span data-ttu-id="250b7-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="250b7-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="250b7-381">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="250b7-381">Get an item by ID</span></span> | <span data-ttu-id="250b7-382">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-382">None</span></span> | <span data-ttu-id="250b7-383">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-383">To-do item</span></span>|
|<span data-ttu-id="250b7-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="250b7-384">POST /api/TodoItems</span></span> | <span data-ttu-id="250b7-385">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-385">Add a new item</span></span> | <span data-ttu-id="250b7-386">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-386">To-do item</span></span> | <span data-ttu-id="250b7-387">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-387">To-do item</span></span> |
|<span data-ttu-id="250b7-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="250b7-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="250b7-389">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="250b7-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="250b7-390">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="250b7-390">To-do item</span></span> | <span data-ttu-id="250b7-391">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-391">None</span></span> |
|<span data-ttu-id="250b7-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="250b7-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="250b7-393">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="250b7-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="250b7-394">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-394">None</span></span> | <span data-ttu-id="250b7-395">なし</span><span class="sxs-lookup"><span data-stu-id="250b7-395">None</span></span>|

<span data-ttu-id="250b7-396">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="250b7-396">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="250b7-402">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="250b7-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-405">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="250b7-406">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="250b7-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-408">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="250b7-409">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="250b7-410">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="250b7-411">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="250b7-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="250b7-412">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="250b7-413">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="250b7-413">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="250b7-416">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="250b7-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="250b7-417">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="250b7-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="250b7-418">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-418">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="250b7-419">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="250b7-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="250b7-420">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-421">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="250b7-422">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-422">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="250b7-424">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="250b7-426">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="250b7-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="250b7-427">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="250b7-429">API のテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-429">Test the API</span></span>

<span data-ttu-id="250b7-430">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-430">The project template creates a `values` API.</span></span> <span data-ttu-id="250b7-431">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="250b7-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="250b7-433">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="250b7-434">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="250b7-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="250b7-435">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="250b7-436">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="250b7-438">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="250b7-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="250b7-439">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="250b7-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="250b7-441">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="250b7-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="250b7-442">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="250b7-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="250b7-443">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="250b7-444">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="250b7-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="250b7-445">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="250b7-446">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-446">Add a model class</span></span>

<span data-ttu-id="250b7-447">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="250b7-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="250b7-448">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="250b7-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-450">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="250b7-451">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="250b7-452">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="250b7-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="250b7-453">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="250b7-454">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="250b7-455">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="250b7-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="250b7-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="250b7-457">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="250b7-458">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="250b7-459">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="250b7-460">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-460">Right-click the project.</span></span> <span data-ttu-id="250b7-461">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="250b7-462">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="250b7-462">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="250b7-464">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="250b7-465">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="250b7-466">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="250b7-467">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="250b7-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="250b7-468">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="250b7-469">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-469">Add a database context</span></span>

<span data-ttu-id="250b7-470">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="250b7-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="250b7-471">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-473">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="250b7-474">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="250b7-475">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="250b7-476">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="250b7-477">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="250b7-478">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="250b7-478">Register the database context</span></span>

<span data-ttu-id="250b7-479">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="250b7-480">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="250b7-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="250b7-481">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="250b7-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="250b7-482">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="250b7-482">The preceding code:</span></span>

* <span data-ttu-id="250b7-483">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="250b7-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="250b7-484">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="250b7-485">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="250b7-486">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-488">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="250b7-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="250b7-489">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="250b7-490">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="250b7-491">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="250b7-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="250b7-494">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="250b7-495">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="250b7-496">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="250b7-496">The preceding code:</span></span>

* <span data-ttu-id="250b7-497">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="250b7-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="250b7-498">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="250b7-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="250b7-499">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="250b7-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="250b7-500">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="250b7-501">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="250b7-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="250b7-502">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="250b7-503">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="250b7-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="250b7-504">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="250b7-505">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="250b7-506">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="250b7-507">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-507">Add Get methods</span></span>

<span data-ttu-id="250b7-508">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="250b7-509">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="250b7-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="250b7-510">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="250b7-510">Stop the app if it's still running.</span></span> <span data-ttu-id="250b7-511">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="250b7-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="250b7-512">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="250b7-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="250b7-513">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="250b7-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="250b7-514">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="250b7-515">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="250b7-515">Routing and URL paths</span></span>

<span data-ttu-id="250b7-516">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="250b7-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="250b7-517">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="250b7-518">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="250b7-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="250b7-519">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="250b7-520">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="250b7-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="250b7-521">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="250b7-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="250b7-522">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="250b7-523">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="250b7-523">This sample doesn't use a template.</span></span> <span data-ttu-id="250b7-524">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="250b7-525">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="250b7-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="250b7-526">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="250b7-527">戻り値</span><span class="sxs-lookup"><span data-stu-id="250b7-527">Return values</span></span>

<span data-ttu-id="250b7-528">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="250b7-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="250b7-529">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="250b7-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="250b7-530">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="250b7-531">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="250b7-532">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="250b7-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="250b7-533">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="250b7-534">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="250b7-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="250b7-535">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="250b7-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="250b7-536">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-536">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="250b7-537">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="250b7-538">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="250b7-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="250b7-539">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="250b7-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="250b7-540">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="250b7-540">Start the web app.</span></span>
* <span data-ttu-id="250b7-541">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="250b7-541">Start Postman.</span></span>
* <span data-ttu-id="250b7-542">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="250b7-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="250b7-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="250b7-544">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="250b7-545">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="250b7-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="250b7-546">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="250b7-547">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="250b7-548">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="250b7-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="250b7-549">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-549">Create a new request.</span></span>
  * <span data-ttu-id="250b7-550">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="250b7-551">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="250b7-552">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="250b7-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="250b7-553">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="250b7-554">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-554">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="250b7-556">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-556">Add a Create method</span></span>

<span data-ttu-id="250b7-557">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="250b7-558">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="250b7-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="250b7-559">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="250b7-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="250b7-560">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="250b7-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="250b7-561">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="250b7-562">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="250b7-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="250b7-563">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="250b7-564">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="250b7-565">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="250b7-566">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="250b7-567">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="250b7-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="250b7-568">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="250b7-569">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="250b7-569">Build the project.</span></span>
* <span data-ttu-id="250b7-570">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="250b7-571">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="250b7-572">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="250b7-573">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="250b7-574">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="250b7-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="250b7-575">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-575">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="250b7-577">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="250b7-578">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-578">Test the location header URI</span></span>

* <span data-ttu-id="250b7-579">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="250b7-580">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="250b7-580">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="250b7-582">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-582">Set the method to GET.</span></span>
* <span data-ttu-id="250b7-583">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="250b7-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="250b7-584">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="250b7-585">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="250b7-586">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="250b7-587">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="250b7-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="250b7-588">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="250b7-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="250b7-589">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="250b7-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="250b7-590">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="250b7-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="250b7-591">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="250b7-592">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="250b7-593">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="250b7-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="250b7-594">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="250b7-595">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="250b7-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="250b7-596">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="250b7-597">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="250b7-597">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="250b7-599">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="250b7-600">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="250b7-601">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="250b7-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="250b7-602">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="250b7-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="250b7-603">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="250b7-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="250b7-604">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="250b7-605">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="250b7-606">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="250b7-606">Select **Send**</span></span>

<span data-ttu-id="250b7-607">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="250b7-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="250b7-608">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="250b7-609">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="250b7-609">Call the web API with JavaScript</span></span>

<span data-ttu-id="250b7-610">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-610">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="250b7-611">Fetch API によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-611">The Fetch API initiates the request.</span></span> <span data-ttu-id="250b7-612">JavaScript により、Web API の応答からの詳細でページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-612">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="250b7-613">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-613">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="250b7-614">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="250b7-614">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="250b7-615">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-615">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="250b7-616">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-616">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="250b7-617">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="250b7-617">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="250b7-618">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="250b7-618">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="250b7-619">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="250b7-619">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="250b7-620">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="250b7-620">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="250b7-621">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="250b7-621">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="250b7-622">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="250b7-622">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="250b7-623">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="250b7-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="250b7-624">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="250b7-624">Get a list of to-do items</span></span>

<span data-ttu-id="250b7-625">Fetch により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-625">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="250b7-626">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="250b7-627">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="250b7-628">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="250b7-628">Add a to-do item</span></span>

<span data-ttu-id="250b7-629">Fetch により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-629">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="250b7-630">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="250b7-631">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="250b7-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="250b7-632">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="250b7-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="250b7-633">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="250b7-633">Update a to-do item</span></span>

<span data-ttu-id="250b7-634">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="250b7-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="250b7-635">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="250b7-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="250b7-636">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="250b7-636">Delete a to-do item</span></span>

<span data-ttu-id="250b7-637">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="250b7-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="250b7-638">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="250b7-638">Additional resources</span></span>

<span data-ttu-id="250b7-639">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="250b7-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="250b7-640">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="250b7-641">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="250b7-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="250b7-642">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="250b7-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
