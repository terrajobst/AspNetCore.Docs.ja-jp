---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 6f2d62600da828261ecfc3a1df688ce914eccf33
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72590016"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="3734a-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="3734a-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="3734a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="3734a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="3734a-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="3734a-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3734a-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3734a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3734a-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="3734a-107">Create a web API project.</span></span>
> * <span data-ttu-id="3734a-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="3734a-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3734a-109">CRUD メソッドを使用してコントローラーをスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="3734a-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="3734a-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="3734a-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="3734a-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="3734a-111">Call the web API with Postman.</span></span>

<span data-ttu-id="3734a-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="3734a-113">概要</span><span class="sxs-lookup"><span data-stu-id="3734a-113">Overview</span></span>

<span data-ttu-id="3734a-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3734a-115">API</span><span class="sxs-lookup"><span data-stu-id="3734a-115">API</span></span> | <span data-ttu-id="3734a-116">説明</span><span class="sxs-lookup"><span data-stu-id="3734a-116">Description</span></span> | <span data-ttu-id="3734a-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="3734a-117">Request body</span></span> | <span data-ttu-id="3734a-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="3734a-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="3734a-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3734a-119">GET /api/TodoItems</span></span> | <span data-ttu-id="3734a-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3734a-120">Get all to-do items</span></span> | <span data-ttu-id="3734a-121">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-121">None</span></span> | <span data-ttu-id="3734a-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="3734a-122">Array of to-do items</span></span>|
|<span data-ttu-id="3734a-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3734a-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="3734a-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3734a-124">Get an item by ID</span></span> | <span data-ttu-id="3734a-125">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-125">None</span></span> | <span data-ttu-id="3734a-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-126">To-do item</span></span>|
|<span data-ttu-id="3734a-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3734a-127">POST /api/TodoItems</span></span> | <span data-ttu-id="3734a-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-128">Add a new item</span></span> | <span data-ttu-id="3734a-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-129">To-do item</span></span> | <span data-ttu-id="3734a-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-130">To-do item</span></span> |
|<span data-ttu-id="3734a-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3734a-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="3734a-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3734a-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3734a-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-133">To-do item</span></span> | <span data-ttu-id="3734a-134">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-134">None</span></span> |
|<span data-ttu-id="3734a-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3734a-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="3734a-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3734a-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3734a-137">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-137">None</span></span> | <span data-ttu-id="3734a-138">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-138">None</span></span>|

<span data-ttu-id="3734a-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="3734a-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3734a-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3734a-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3734a-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="3734a-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-151">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3734a-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3734a-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3734a-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3734a-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="3734a-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-155">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3734a-158">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3734a-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3734a-159">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="3734a-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3734a-160">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="3734a-161">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="3734a-162">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3734a-162">The preceding commands:</span></span>

  * <span data-ttu-id="3734a-163">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="3734a-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="3734a-164">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3734a-166">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-166">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3734a-168">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="3734a-170">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.0]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="3734a-171">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="3734a-173">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="3734a-174">API のテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-174">Test the API</span></span>

<span data-ttu-id="3734a-175">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="3734a-176">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3734a-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3734a-178">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3734a-179">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3734a-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3734a-180">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3734a-181">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3734a-183">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3734a-184">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="3734a-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3734a-186">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3734a-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3734a-187">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3734a-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3734a-188">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3734a-189">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="3734a-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="3734a-190">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="3734a-191">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-191">Add a model class</span></span>

<span data-ttu-id="3734a-192">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="3734a-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3734a-193">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="3734a-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-195">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3734a-196">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3734a-197">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3734a-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="3734a-198">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3734a-199">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3734a-200">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3734a-202">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3734a-203">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3734a-205">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-205">Right-click the project.</span></span> <span data-ttu-id="3734a-206">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3734a-207">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3734a-207">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3734a-209">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[Empty Class]\(空のクラス)** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3734a-210">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3734a-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="3734a-212">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="3734a-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3734a-213">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3734a-214">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-214">Add a database context</span></span>

<span data-ttu-id="3734a-215">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="3734a-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3734a-216">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="3734a-218">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="3734a-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="3734a-219">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="3734a-220">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="3734a-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="3734a-221">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="3734a-222">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="3734a-223">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="3734a-225">TodoContext データベースコンテキストを追加する</span><span class="sxs-lookup"><span data-stu-id="3734a-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="3734a-226">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3734a-227">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3734a-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3734a-229">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3734a-230">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="3734a-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3734a-231">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="3734a-231">Register the database context</span></span>

<span data-ttu-id="3734a-232">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3734a-233">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3734a-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="3734a-234">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="3734a-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="3734a-235">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3734a-235">The preceding code:</span></span>

* <span data-ttu-id="3734a-236">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="3734a-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3734a-237">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3734a-238">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="3734a-239">コントローラーをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="3734a-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-241">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3734a-242">**[追加]** > **[新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3734a-243">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="3734a-244">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="3734a-245">**モデル クラス**で **[TodoItem (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="3734a-246">**データ コンテキスト クラス**で **[TodoContext (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="3734a-247">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3734a-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3734a-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3734a-249">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="3734a-250">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3734a-250">The preceding commands:</span></span>

* <span data-ttu-id="3734a-251">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="3734a-252">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3734a-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="3734a-253">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="3734a-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="3734a-254">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3734a-254">The generated code:</span></span>

* <span data-ttu-id="3734a-255">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="3734a-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="3734a-256">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="3734a-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="3734a-257">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="3734a-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3734a-258">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3734a-259">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3734a-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3734a-260">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="3734a-261">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="3734a-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="3734a-262">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="3734a-263">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="3734a-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="3734a-264">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="3734a-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3734a-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="3734a-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="3734a-266">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="3734a-267">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3734a-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3734a-268">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="3734a-269">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="3734a-270">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3734a-271">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3734a-272">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="3734a-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="3734a-273">Postman をインストールする</span><span class="sxs-lookup"><span data-stu-id="3734a-273">Install Postman</span></span>

<span data-ttu-id="3734a-274">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="3734a-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3734a-275">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3734a-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="3734a-276">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3734a-276">Start the web app.</span></span>
* <span data-ttu-id="3734a-277">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="3734a-277">Start Postman.</span></span>
* <span data-ttu-id="3734a-278">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="3734a-279">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="3734a-280">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="3734a-281">Postman を使用して PostTodoItem をテストする</span><span class="sxs-lookup"><span data-stu-id="3734a-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="3734a-282">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-282">Create a new request.</span></span>
* <span data-ttu-id="3734a-283">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3734a-284">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="3734a-285">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3734a-286">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3734a-287">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="3734a-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3734a-288">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-288">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="3734a-290">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-290">Test the location header URI</span></span>

* <span data-ttu-id="3734a-291">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3734a-292">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="3734a-292">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="3734a-294">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-294">Set the method to GET.</span></span>
* <span data-ttu-id="3734a-295">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="3734a-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3734a-296">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="3734a-297">GET メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="3734a-297">Examine the GET methods</span></span>

<span data-ttu-id="3734a-298">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="3734a-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="3734a-299">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3734a-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="3734a-300">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3734a-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="3734a-301">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="3734a-302">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="3734a-302">Test Get with Postman</span></span>

* <span data-ttu-id="3734a-303">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-303">Create a new request.</span></span>
* <span data-ttu-id="3734a-304">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="3734a-305">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3734a-306">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="3734a-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3734a-307">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3734a-308">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-308">Select **Send**.</span></span>

<span data-ttu-id="3734a-309">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-309">This app uses an in-memory database.</span></span> <span data-ttu-id="3734a-310">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="3734a-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="3734a-311">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="3734a-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="3734a-312">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="3734a-312">Routing and URL paths</span></span>

<span data-ttu-id="3734a-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="3734a-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3734a-314">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3734a-315">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="3734a-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="3734a-316">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3734a-317">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="3734a-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="3734a-318">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="3734a-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3734a-319">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3734a-320">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3734a-320">This sample doesn't use a template.</span></span> <span data-ttu-id="3734a-321">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3734a-322">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="3734a-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3734a-323">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3734a-324">戻り値</span><span class="sxs-lookup"><span data-stu-id="3734a-324">Return values</span></span>

<span data-ttu-id="3734a-325">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="3734a-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3734a-326">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="3734a-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3734a-327">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3734a-328">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3734a-329">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="3734a-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3734a-330">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3734a-331">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="3734a-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="3734a-332">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="3734a-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3734a-333">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="3734a-334">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="3734a-334">The PutTodoItem method</span></span>

<span data-ttu-id="3734a-335">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="3734a-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="3734a-336">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="3734a-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3734a-337">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3734a-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3734a-338">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="3734a-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3734a-339">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="3734a-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3734a-340">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3734a-341">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="3734a-342">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="3734a-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3734a-343">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3734a-344">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3734a-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3734a-345">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3734a-346">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="3734a-346">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="3734a-348">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="3734a-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="3734a-349">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="3734a-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="3734a-350">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3734a-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3734a-351">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3734a-352">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="3734a-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3734a-353">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3734a-354">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-354">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3734a-355">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-355">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3734a-356">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="3734a-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="3734a-357">手順については、「[チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3734a-358">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3734a-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3734a-359">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="3734a-359">Create a web API project.</span></span>
> * <span data-ttu-id="3734a-360">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="3734a-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3734a-361">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="3734a-361">Add a controller.</span></span>
> * <span data-ttu-id="3734a-362">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="3734a-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="3734a-363">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="3734a-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="3734a-364">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="3734a-364">Specify return values.</span></span>
> * <span data-ttu-id="3734a-365">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="3734a-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="3734a-366">JavaScript で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="3734a-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="3734a-367">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="3734a-368">概要</span><span class="sxs-lookup"><span data-stu-id="3734a-368">Overview</span></span>

<span data-ttu-id="3734a-369">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3734a-370">API</span><span class="sxs-lookup"><span data-stu-id="3734a-370">API</span></span> | <span data-ttu-id="3734a-371">説明</span><span class="sxs-lookup"><span data-stu-id="3734a-371">Description</span></span> | <span data-ttu-id="3734a-372">要求本文</span><span class="sxs-lookup"><span data-stu-id="3734a-372">Request body</span></span> | <span data-ttu-id="3734a-373">応答本文</span><span class="sxs-lookup"><span data-stu-id="3734a-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="3734a-374">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3734a-374">GET /api/TodoItems</span></span> | <span data-ttu-id="3734a-375">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3734a-375">Get all to-do items</span></span> | <span data-ttu-id="3734a-376">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-376">None</span></span> | <span data-ttu-id="3734a-377">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="3734a-377">Array of to-do items</span></span>|
|<span data-ttu-id="3734a-378">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3734a-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="3734a-379">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3734a-379">Get an item by ID</span></span> | <span data-ttu-id="3734a-380">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-380">None</span></span> | <span data-ttu-id="3734a-381">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-381">To-do item</span></span>|
|<span data-ttu-id="3734a-382">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3734a-382">POST /api/TodoItems</span></span> | <span data-ttu-id="3734a-383">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-383">Add a new item</span></span> | <span data-ttu-id="3734a-384">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-384">To-do item</span></span> | <span data-ttu-id="3734a-385">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-385">To-do item</span></span> |
|<span data-ttu-id="3734a-386">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3734a-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="3734a-387">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3734a-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3734a-388">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3734a-388">To-do item</span></span> | <span data-ttu-id="3734a-389">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-389">None</span></span> |
|<span data-ttu-id="3734a-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3734a-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="3734a-391">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3734a-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3734a-392">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-392">None</span></span> | <span data-ttu-id="3734a-393">なし</span><span class="sxs-lookup"><span data-stu-id="3734a-393">None</span></span>|

<span data-ttu-id="3734a-394">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="3734a-394">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3734a-400">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3734a-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-403">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3734a-404">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="3734a-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-406">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3734a-407">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3734a-408">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3734a-409">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3734a-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="3734a-410">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="3734a-411">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="3734a-411">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3734a-414">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3734a-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3734a-415">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="3734a-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3734a-416">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="3734a-417">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="3734a-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="3734a-418">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-419">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3734a-420">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-420">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3734a-422">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="3734a-424">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="3734a-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="3734a-425">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="3734a-427">API のテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-427">Test the API</span></span>

<span data-ttu-id="3734a-428">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-428">The project template creates a `values` API.</span></span> <span data-ttu-id="3734a-429">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3734a-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3734a-431">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3734a-432">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3734a-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3734a-433">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3734a-434">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3734a-436">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3734a-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3734a-437">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="3734a-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3734a-439">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3734a-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3734a-440">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3734a-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3734a-441">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3734a-442">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="3734a-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="3734a-443">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="3734a-444">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-444">Add a model class</span></span>

<span data-ttu-id="3734a-445">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="3734a-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3734a-446">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="3734a-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-448">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3734a-449">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3734a-450">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3734a-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="3734a-451">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3734a-452">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3734a-453">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3734a-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3734a-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3734a-455">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3734a-456">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3734a-457">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3734a-458">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-458">Right-click the project.</span></span> <span data-ttu-id="3734a-459">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3734a-460">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3734a-460">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3734a-462">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[Empty Class]\(空のクラス)** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3734a-463">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3734a-464">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="3734a-465">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="3734a-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3734a-466">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3734a-467">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-467">Add a database context</span></span>

<span data-ttu-id="3734a-468">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="3734a-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3734a-469">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-471">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3734a-472">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3734a-473">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3734a-474">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3734a-475">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3734a-476">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="3734a-476">Register the database context</span></span>

<span data-ttu-id="3734a-477">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3734a-478">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3734a-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="3734a-479">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="3734a-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="3734a-480">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3734a-480">The preceding code:</span></span>

* <span data-ttu-id="3734a-481">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="3734a-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3734a-482">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3734a-483">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3734a-484">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-486">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3734a-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3734a-487">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="3734a-488">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="3734a-489">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3734a-491">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3734a-492">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="3734a-493">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="3734a-494">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3734a-494">The preceding code:</span></span>

* <span data-ttu-id="3734a-495">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="3734a-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="3734a-496">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="3734a-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="3734a-497">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="3734a-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3734a-498">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3734a-499">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3734a-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3734a-500">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="3734a-501">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="3734a-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="3734a-502">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="3734a-503">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="3734a-504">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="3734a-505">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-505">Add Get methods</span></span>

<span data-ttu-id="3734a-506">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="3734a-507">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="3734a-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="3734a-508">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="3734a-508">Stop the app if it's still running.</span></span> <span data-ttu-id="3734a-509">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="3734a-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="3734a-510">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3734a-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="3734a-511">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3734a-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="3734a-512">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="3734a-513">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="3734a-513">Routing and URL paths</span></span>

<span data-ttu-id="3734a-514">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="3734a-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3734a-515">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3734a-516">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="3734a-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="3734a-517">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3734a-518">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="3734a-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="3734a-519">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="3734a-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3734a-520">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3734a-521">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3734a-521">This sample doesn't use a template.</span></span> <span data-ttu-id="3734a-522">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3734a-523">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="3734a-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3734a-524">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3734a-525">戻り値</span><span class="sxs-lookup"><span data-stu-id="3734a-525">Return values</span></span>

<span data-ttu-id="3734a-526">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="3734a-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3734a-527">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="3734a-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3734a-528">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3734a-529">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3734a-530">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="3734a-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3734a-531">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3734a-532">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="3734a-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="3734a-533">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="3734a-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3734a-534">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="3734a-535">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="3734a-536">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="3734a-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3734a-537">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3734a-537">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="3734a-538">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3734a-538">Start the web app.</span></span>
* <span data-ttu-id="3734a-539">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="3734a-539">Start Postman.</span></span>
* <span data-ttu-id="3734a-540">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-540">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3734a-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3734a-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3734a-542">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3734a-543">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3734a-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3734a-544">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="3734a-545">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="3734a-546">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="3734a-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="3734a-547">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-547">Create a new request.</span></span>
  * <span data-ttu-id="3734a-548">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="3734a-549">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="3734a-550">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="3734a-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="3734a-551">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3734a-552">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-552">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="3734a-554">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-554">Add a Create method</span></span>

<span data-ttu-id="3734a-555">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="3734a-556">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="3734a-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="3734a-557">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="3734a-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3734a-558">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="3734a-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="3734a-559">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="3734a-560">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3734a-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3734a-561">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="3734a-562">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="3734a-563">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3734a-564">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3734a-565">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="3734a-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="3734a-566">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="3734a-567">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3734a-567">Build the project.</span></span>
* <span data-ttu-id="3734a-568">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3734a-569">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="3734a-570">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3734a-571">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3734a-572">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="3734a-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3734a-573">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-573">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="3734a-575">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="3734a-576">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-576">Test the location header URI</span></span>

* <span data-ttu-id="3734a-577">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3734a-578">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="3734a-578">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="3734a-580">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-580">Set the method to GET.</span></span>
* <span data-ttu-id="3734a-581">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="3734a-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="3734a-582">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="3734a-583">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="3734a-584">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="3734a-585">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="3734a-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3734a-586">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3734a-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3734a-587">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="3734a-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3734a-588">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="3734a-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3734a-589">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3734a-590">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="3734a-591">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="3734a-591">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3734a-592">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3734a-593">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3734a-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3734a-594">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3734a-595">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="3734a-595">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="3734a-597">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="3734a-598">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="3734a-599">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3734a-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3734a-600">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3734a-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3734a-601">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="3734a-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3734a-602">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3734a-603">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-603">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="3734a-604">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3734a-604">Select **Send**.</span></span>

<span data-ttu-id="3734a-605">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="3734a-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="3734a-606">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3734a-607">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="3734a-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="3734a-608">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="3734a-609">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-609">jQuery initiates the request.</span></span> <span data-ttu-id="3734a-610">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="3734a-611">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="3734a-612">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3734a-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="3734a-613">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="3734a-614">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="3734a-615">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3734a-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="3734a-616">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3734a-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="3734a-617">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="3734a-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="3734a-618">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="3734a-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="3734a-619">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="3734a-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="3734a-620">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3734a-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="3734a-621">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3734a-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="3734a-622">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="3734a-622">Get a list of to-do items</span></span>

<span data-ttu-id="3734a-623">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-623">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="3734a-624">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="3734a-625">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="3734a-626">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="3734a-626">Add a to-do item</span></span>

<span data-ttu-id="3734a-627">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-627">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="3734a-628">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="3734a-629">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="3734a-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="3734a-630">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="3734a-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="3734a-631">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="3734a-631">Update a to-do item</span></span>

<span data-ttu-id="3734a-632">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="3734a-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="3734a-633">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="3734a-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="3734a-634">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="3734a-634">Delete a to-do item</span></span>

<span data-ttu-id="3734a-635">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="3734a-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="3734a-636">Web API に認証サポートを追加する</span><span class="sxs-lookup"><span data-stu-id="3734a-636">Add authentication support to a web API</span></span>

<span data-ttu-id="3734a-637">[IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) のチュートリアルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3734a-638">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3734a-638">Additional resources</span></span>

<span data-ttu-id="3734a-639">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="3734a-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="3734a-640">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="3734a-641">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3734a-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="3734a-642">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="3734a-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
