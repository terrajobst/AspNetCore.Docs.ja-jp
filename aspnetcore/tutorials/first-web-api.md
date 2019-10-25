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
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="86a82-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="86a82-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="86a82-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="86a82-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="86a82-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="86a82-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86a82-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="86a82-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="86a82-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="86a82-107">Create a web API project.</span></span>
> * <span data-ttu-id="86a82-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="86a82-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="86a82-109">CRUD メソッドを使用してコントローラーをスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="86a82-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="86a82-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="86a82-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="86a82-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="86a82-111">Call the web API with Postman.</span></span>

<span data-ttu-id="86a82-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="86a82-113">概要</span><span class="sxs-lookup"><span data-stu-id="86a82-113">Overview</span></span>

<span data-ttu-id="86a82-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="86a82-115">API</span><span class="sxs-lookup"><span data-stu-id="86a82-115">API</span></span> | <span data-ttu-id="86a82-116">説明</span><span class="sxs-lookup"><span data-stu-id="86a82-116">Description</span></span> | <span data-ttu-id="86a82-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="86a82-117">Request body</span></span> | <span data-ttu-id="86a82-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="86a82-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="86a82-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="86a82-119">GET /api/TodoItems</span></span> | <span data-ttu-id="86a82-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="86a82-120">Get all to-do items</span></span> | <span data-ttu-id="86a82-121">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-121">None</span></span> | <span data-ttu-id="86a82-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="86a82-122">Array of to-do items</span></span>|
|<span data-ttu-id="86a82-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="86a82-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="86a82-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="86a82-124">Get an item by ID</span></span> | <span data-ttu-id="86a82-125">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-125">None</span></span> | <span data-ttu-id="86a82-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-126">To-do item</span></span>|
|<span data-ttu-id="86a82-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="86a82-127">POST /api/TodoItems</span></span> | <span data-ttu-id="86a82-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-128">Add a new item</span></span> | <span data-ttu-id="86a82-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-129">To-do item</span></span> | <span data-ttu-id="86a82-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-130">To-do item</span></span> |
|<span data-ttu-id="86a82-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="86a82-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="86a82-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="86a82-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="86a82-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-133">To-do item</span></span> | <span data-ttu-id="86a82-134">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-134">None</span></span> |
|<span data-ttu-id="86a82-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="86a82-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="86a82-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="86a82-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="86a82-137">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-137">None</span></span> | <span data-ttu-id="86a82-138">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-138">None</span></span>|

<span data-ttu-id="86a82-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="86a82-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="86a82-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="86a82-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="86a82-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="86a82-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-151">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="86a82-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="86a82-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="86a82-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="86a82-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="86a82-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-155">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="86a82-158">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="86a82-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="86a82-159">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="86a82-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="86a82-160">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="86a82-161">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="86a82-162">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86a82-162">The preceding commands:</span></span>

  * <span data-ttu-id="86a82-163">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="86a82-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="86a82-164">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="86a82-166">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-166">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="86a82-168">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="86a82-170">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.0]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="86a82-171">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="86a82-173">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="86a82-174">API のテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-174">Test the API</span></span>

<span data-ttu-id="86a82-175">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="86a82-176">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="86a82-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="86a82-178">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="86a82-179">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="86a82-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="86a82-180">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="86a82-181">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="86a82-183">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="86a82-184">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="86a82-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="86a82-186">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="86a82-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="86a82-187">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="86a82-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="86a82-188">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="86a82-189">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="86a82-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="86a82-190">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="86a82-191">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-191">Add a model class</span></span>

<span data-ttu-id="86a82-192">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="86a82-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="86a82-193">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="86a82-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-195">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="86a82-196">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="86a82-197">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="86a82-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="86a82-198">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="86a82-199">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="86a82-200">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="86a82-202">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="86a82-203">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="86a82-205">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-205">Right-click the project.</span></span> <span data-ttu-id="86a82-206">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="86a82-207">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="86a82-207">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="86a82-209">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[Empty Class]\(空のクラス)** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="86a82-210">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="86a82-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="86a82-212">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="86a82-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="86a82-213">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="86a82-214">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-214">Add a database context</span></span>

<span data-ttu-id="86a82-215">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="86a82-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="86a82-216">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="86a82-218">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="86a82-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="86a82-219">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="86a82-220">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="86a82-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="86a82-221">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="86a82-222">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="86a82-223">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="86a82-225">TodoContext データベースコンテキストを追加する</span><span class="sxs-lookup"><span data-stu-id="86a82-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="86a82-226">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="86a82-227">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="86a82-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="86a82-229">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="86a82-230">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="86a82-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="86a82-231">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="86a82-231">Register the database context</span></span>

<span data-ttu-id="86a82-232">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="86a82-233">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="86a82-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="86a82-234">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="86a82-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="86a82-235">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86a82-235">The preceding code:</span></span>

* <span data-ttu-id="86a82-236">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="86a82-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="86a82-237">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="86a82-238">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="86a82-239">コントローラーをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="86a82-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-241">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="86a82-242">**[追加]** > **[新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="86a82-243">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="86a82-244">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="86a82-245">**モデル クラス**で **[TodoItem (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="86a82-246">**データ コンテキスト クラス**で **[TodoContext (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="86a82-247">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="86a82-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="86a82-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="86a82-249">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="86a82-250">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86a82-250">The preceding commands:</span></span>

* <span data-ttu-id="86a82-251">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="86a82-252">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="86a82-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="86a82-253">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="86a82-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="86a82-254">生成されたコード:</span><span class="sxs-lookup"><span data-stu-id="86a82-254">The generated code:</span></span>

* <span data-ttu-id="86a82-255">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="86a82-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="86a82-256">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="86a82-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="86a82-257">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="86a82-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="86a82-258">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="86a82-259">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="86a82-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="86a82-260">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="86a82-261">PostTodoItem create 作成メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="86a82-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="86a82-262">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="86a82-263">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="86a82-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="86a82-264">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="86a82-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="86a82-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="86a82-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="86a82-266">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="86a82-267">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="86a82-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="86a82-268">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="86a82-269">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="86a82-270">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="86a82-271">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="86a82-272">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="86a82-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="86a82-273">Postman をインストールする</span><span class="sxs-lookup"><span data-stu-id="86a82-273">Install Postman</span></span>

<span data-ttu-id="86a82-274">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="86a82-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="86a82-275">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="86a82-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="86a82-276">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="86a82-276">Start the web app.</span></span>
* <span data-ttu-id="86a82-277">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="86a82-277">Start Postman.</span></span>
* <span data-ttu-id="86a82-278">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="86a82-279">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="86a82-280">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="86a82-281">Postman を使用して PostTodoItem をテストする</span><span class="sxs-lookup"><span data-stu-id="86a82-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="86a82-282">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-282">Create a new request.</span></span>
* <span data-ttu-id="86a82-283">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="86a82-284">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="86a82-285">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="86a82-286">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="86a82-287">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="86a82-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="86a82-288">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-288">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="86a82-290">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-290">Test the location header URI</span></span>

* <span data-ttu-id="86a82-291">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="86a82-292">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="86a82-292">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="86a82-294">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-294">Set the method to GET.</span></span>
* <span data-ttu-id="86a82-295">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="86a82-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="86a82-296">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="86a82-297">GET メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="86a82-297">Examine the GET methods</span></span>

<span data-ttu-id="86a82-298">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="86a82-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="86a82-299">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="86a82-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="86a82-300">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="86a82-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="86a82-301">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="86a82-302">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="86a82-302">Test Get with Postman</span></span>

* <span data-ttu-id="86a82-303">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-303">Create a new request.</span></span>
* <span data-ttu-id="86a82-304">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="86a82-305">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="86a82-306">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="86a82-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="86a82-307">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="86a82-308">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-308">Select **Send**.</span></span>

<span data-ttu-id="86a82-309">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-309">This app uses an in-memory database.</span></span> <span data-ttu-id="86a82-310">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="86a82-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="86a82-311">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="86a82-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="86a82-312">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="86a82-312">Routing and URL paths</span></span>

<span data-ttu-id="86a82-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="86a82-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="86a82-314">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="86a82-315">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="86a82-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="86a82-316">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="86a82-317">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="86a82-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="86a82-318">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="86a82-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="86a82-319">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="86a82-320">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="86a82-320">This sample doesn't use a template.</span></span> <span data-ttu-id="86a82-321">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="86a82-322">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="86a82-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="86a82-323">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="86a82-324">戻り値</span><span class="sxs-lookup"><span data-stu-id="86a82-324">Return values</span></span>

<span data-ttu-id="86a82-325">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="86a82-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="86a82-326">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="86a82-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="86a82-327">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="86a82-328">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="86a82-329">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="86a82-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="86a82-330">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="86a82-331">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="86a82-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="86a82-332">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="86a82-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="86a82-333">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="86a82-334">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="86a82-334">The PutTodoItem method</span></span>

<span data-ttu-id="86a82-335">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="86a82-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="86a82-336">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="86a82-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="86a82-337">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="86a82-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="86a82-338">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="86a82-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="86a82-339">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="86a82-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="86a82-340">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="86a82-341">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="86a82-342">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="86a82-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="86a82-343">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="86a82-344">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="86a82-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="86a82-345">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="86a82-346">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="86a82-346">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="86a82-348">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="86a82-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="86a82-349">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="86a82-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="86a82-350">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="86a82-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="86a82-351">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="86a82-352">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="86a82-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="86a82-353">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="86a82-354">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-354">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="86a82-355">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-355">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="86a82-356">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="86a82-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="86a82-357">手順については、「[チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86a82-358">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="86a82-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="86a82-359">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="86a82-359">Create a web API project.</span></span>
> * <span data-ttu-id="86a82-360">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="86a82-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="86a82-361">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="86a82-361">Add a controller.</span></span>
> * <span data-ttu-id="86a82-362">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="86a82-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="86a82-363">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="86a82-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="86a82-364">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="86a82-364">Specify return values.</span></span>
> * <span data-ttu-id="86a82-365">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="86a82-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="86a82-366">JavaScript で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="86a82-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="86a82-367">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="86a82-368">概要</span><span class="sxs-lookup"><span data-stu-id="86a82-368">Overview</span></span>

<span data-ttu-id="86a82-369">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="86a82-370">API</span><span class="sxs-lookup"><span data-stu-id="86a82-370">API</span></span> | <span data-ttu-id="86a82-371">説明</span><span class="sxs-lookup"><span data-stu-id="86a82-371">Description</span></span> | <span data-ttu-id="86a82-372">要求本文</span><span class="sxs-lookup"><span data-stu-id="86a82-372">Request body</span></span> | <span data-ttu-id="86a82-373">応答本文</span><span class="sxs-lookup"><span data-stu-id="86a82-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="86a82-374">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="86a82-374">GET /api/TodoItems</span></span> | <span data-ttu-id="86a82-375">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="86a82-375">Get all to-do items</span></span> | <span data-ttu-id="86a82-376">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-376">None</span></span> | <span data-ttu-id="86a82-377">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="86a82-377">Array of to-do items</span></span>|
|<span data-ttu-id="86a82-378">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="86a82-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="86a82-379">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="86a82-379">Get an item by ID</span></span> | <span data-ttu-id="86a82-380">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-380">None</span></span> | <span data-ttu-id="86a82-381">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-381">To-do item</span></span>|
|<span data-ttu-id="86a82-382">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="86a82-382">POST /api/TodoItems</span></span> | <span data-ttu-id="86a82-383">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-383">Add a new item</span></span> | <span data-ttu-id="86a82-384">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-384">To-do item</span></span> | <span data-ttu-id="86a82-385">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-385">To-do item</span></span> |
|<span data-ttu-id="86a82-386">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="86a82-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="86a82-387">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="86a82-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="86a82-388">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="86a82-388">To-do item</span></span> | <span data-ttu-id="86a82-389">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-389">None</span></span> |
|<span data-ttu-id="86a82-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="86a82-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="86a82-391">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="86a82-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="86a82-392">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-392">None</span></span> | <span data-ttu-id="86a82-393">なし</span><span class="sxs-lookup"><span data-stu-id="86a82-393">None</span></span>|

<span data-ttu-id="86a82-394">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="86a82-394">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="86a82-400">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="86a82-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-403">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="86a82-404">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="86a82-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-406">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="86a82-407">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="86a82-408">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="86a82-409">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="86a82-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="86a82-410">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="86a82-411">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="86a82-411">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="86a82-414">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="86a82-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="86a82-415">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="86a82-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="86a82-416">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="86a82-417">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="86a82-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="86a82-418">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-419">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="86a82-420">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-420">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="86a82-422">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="86a82-424">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="86a82-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="86a82-425">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="86a82-427">API のテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-427">Test the API</span></span>

<span data-ttu-id="86a82-428">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-428">The project template creates a `values` API.</span></span> <span data-ttu-id="86a82-429">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="86a82-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="86a82-431">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="86a82-432">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="86a82-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="86a82-433">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="86a82-434">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="86a82-436">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="86a82-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="86a82-437">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="86a82-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="86a82-439">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="86a82-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="86a82-440">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="86a82-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="86a82-441">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="86a82-442">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="86a82-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="86a82-443">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="86a82-444">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-444">Add a model class</span></span>

<span data-ttu-id="86a82-445">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="86a82-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="86a82-446">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="86a82-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-448">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="86a82-449">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="86a82-450">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="86a82-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="86a82-451">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="86a82-452">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="86a82-453">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="86a82-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="86a82-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="86a82-455">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="86a82-456">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="86a82-457">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="86a82-458">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-458">Right-click the project.</span></span> <span data-ttu-id="86a82-459">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="86a82-460">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="86a82-460">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="86a82-462">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[Empty Class]\(空のクラス)** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="86a82-463">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="86a82-464">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="86a82-465">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="86a82-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="86a82-466">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="86a82-467">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-467">Add a database context</span></span>

<span data-ttu-id="86a82-468">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="86a82-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="86a82-469">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-471">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="86a82-472">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="86a82-473">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="86a82-474">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="86a82-475">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="86a82-476">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="86a82-476">Register the database context</span></span>

<span data-ttu-id="86a82-477">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="86a82-478">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="86a82-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="86a82-479">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="86a82-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="86a82-480">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86a82-480">The preceding code:</span></span>

* <span data-ttu-id="86a82-481">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="86a82-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="86a82-482">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="86a82-483">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="86a82-484">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-486">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="86a82-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="86a82-487">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="86a82-488">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="86a82-489">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="86a82-491">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="86a82-492">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="86a82-493">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="86a82-494">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86a82-494">The preceding code:</span></span>

* <span data-ttu-id="86a82-495">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="86a82-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="86a82-496">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="86a82-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="86a82-497">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="86a82-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="86a82-498">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="86a82-499">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="86a82-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="86a82-500">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="86a82-501">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="86a82-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="86a82-502">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="86a82-503">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="86a82-504">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="86a82-505">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-505">Add Get methods</span></span>

<span data-ttu-id="86a82-506">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="86a82-507">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="86a82-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="86a82-508">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="86a82-508">Stop the app if it's still running.</span></span> <span data-ttu-id="86a82-509">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="86a82-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="86a82-510">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="86a82-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="86a82-511">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="86a82-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="86a82-512">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="86a82-513">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="86a82-513">Routing and URL paths</span></span>

<span data-ttu-id="86a82-514">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="86a82-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="86a82-515">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="86a82-516">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="86a82-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="86a82-517">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="86a82-518">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="86a82-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="86a82-519">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="86a82-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="86a82-520">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="86a82-521">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="86a82-521">This sample doesn't use a template.</span></span> <span data-ttu-id="86a82-522">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="86a82-523">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="86a82-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="86a82-524">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="86a82-525">戻り値</span><span class="sxs-lookup"><span data-stu-id="86a82-525">Return values</span></span>

<span data-ttu-id="86a82-526">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="86a82-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="86a82-527">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="86a82-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="86a82-528">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="86a82-529">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="86a82-530">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="86a82-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="86a82-531">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="86a82-532">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="86a82-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="86a82-533">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="86a82-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="86a82-534">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="86a82-535">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="86a82-536">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="86a82-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="86a82-537">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="86a82-537">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="86a82-538">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="86a82-538">Start the web app.</span></span>
* <span data-ttu-id="86a82-539">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="86a82-539">Start Postman.</span></span>
* <span data-ttu-id="86a82-540">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-540">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="86a82-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86a82-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="86a82-542">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="86a82-543">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="86a82-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="86a82-544">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="86a82-545">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="86a82-546">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="86a82-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="86a82-547">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-547">Create a new request.</span></span>
  * <span data-ttu-id="86a82-548">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="86a82-549">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="86a82-550">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="86a82-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="86a82-551">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="86a82-552">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-552">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="86a82-554">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-554">Add a Create method</span></span>

<span data-ttu-id="86a82-555">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="86a82-556">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="86a82-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="86a82-557">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="86a82-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="86a82-558">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="86a82-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="86a82-559">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="86a82-560">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="86a82-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="86a82-561">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="86a82-562">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="86a82-563">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="86a82-564">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="86a82-565">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="86a82-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="86a82-566">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="86a82-567">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="86a82-567">Build the project.</span></span>
* <span data-ttu-id="86a82-568">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="86a82-569">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="86a82-570">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="86a82-571">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="86a82-572">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="86a82-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="86a82-573">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-573">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="86a82-575">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="86a82-576">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-576">Test the location header URI</span></span>

* <span data-ttu-id="86a82-577">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="86a82-578">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="86a82-578">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="86a82-580">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-580">Set the method to GET.</span></span>
* <span data-ttu-id="86a82-581">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="86a82-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="86a82-582">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="86a82-583">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="86a82-584">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="86a82-585">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="86a82-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="86a82-586">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="86a82-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="86a82-587">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="86a82-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="86a82-588">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="86a82-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="86a82-589">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="86a82-590">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="86a82-591">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="86a82-591">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="86a82-592">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="86a82-593">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="86a82-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="86a82-594">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="86a82-595">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="86a82-595">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="86a82-597">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="86a82-598">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="86a82-599">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="86a82-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="86a82-600">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="86a82-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="86a82-601">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="86a82-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="86a82-602">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="86a82-603">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-603">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="86a82-604">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="86a82-604">Select **Send**.</span></span>

<span data-ttu-id="86a82-605">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="86a82-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="86a82-606">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="86a82-607">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="86a82-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="86a82-608">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="86a82-609">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-609">jQuery initiates the request.</span></span> <span data-ttu-id="86a82-610">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="86a82-611">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="86a82-612">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="86a82-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="86a82-613">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="86a82-614">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="86a82-615">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="86a82-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="86a82-616">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="86a82-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="86a82-617">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="86a82-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="86a82-618">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="86a82-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="86a82-619">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="86a82-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="86a82-620">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="86a82-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="86a82-621">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="86a82-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="86a82-622">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="86a82-622">Get a list of to-do items</span></span>

<span data-ttu-id="86a82-623">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-623">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="86a82-624">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="86a82-625">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="86a82-626">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="86a82-626">Add a to-do item</span></span>

<span data-ttu-id="86a82-627">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-627">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="86a82-628">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="86a82-629">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="86a82-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="86a82-630">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="86a82-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="86a82-631">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="86a82-631">Update a to-do item</span></span>

<span data-ttu-id="86a82-632">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="86a82-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="86a82-633">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="86a82-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="86a82-634">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="86a82-634">Delete a to-do item</span></span>

<span data-ttu-id="86a82-635">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="86a82-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="86a82-636">Web API に認証サポートを追加する</span><span class="sxs-lookup"><span data-stu-id="86a82-636">Add authentication support to a web API</span></span>

<span data-ttu-id="86a82-637">[IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) のチュートリアルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86a82-638">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="86a82-638">Additional resources</span></span>

<span data-ttu-id="86a82-639">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="86a82-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="86a82-640">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="86a82-641">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a82-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="86a82-642">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="86a82-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
