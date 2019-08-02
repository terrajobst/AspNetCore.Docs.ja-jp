---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 60235af56077127093ac1d77338bc228a6edf073
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602525"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="83d8e-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="83d8e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="83d8e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="83d8e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="83d8e-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83d8e-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="83d8e-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-107">Create a web API project.</span></span>
> * <span data-ttu-id="83d8e-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="83d8e-109">CRUD メソッドを使用してコントローラーをスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="83d8e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="83d8e-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="83d8e-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="83d8e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="83d8e-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="83d8e-113">概要</span><span class="sxs-lookup"><span data-stu-id="83d8e-113">Overview</span></span>

<span data-ttu-id="83d8e-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="83d8e-115">API</span><span class="sxs-lookup"><span data-stu-id="83d8e-115">API</span></span> | <span data-ttu-id="83d8e-116">説明</span><span class="sxs-lookup"><span data-stu-id="83d8e-116">Description</span></span> | <span data-ttu-id="83d8e-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="83d8e-117">Request body</span></span> | <span data-ttu-id="83d8e-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="83d8e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="83d8e-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="83d8e-119">GET /api/TodoItems</span></span> | <span data-ttu-id="83d8e-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-120">Get all to-do items</span></span> | <span data-ttu-id="83d8e-121">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-121">None</span></span> | <span data-ttu-id="83d8e-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="83d8e-122">Array of to-do items</span></span>|
|<span data-ttu-id="83d8e-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="83d8e-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="83d8e-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-124">Get an item by ID</span></span> | <span data-ttu-id="83d8e-125">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-125">None</span></span> | <span data-ttu-id="83d8e-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-126">To-do item</span></span>|
|<span data-ttu-id="83d8e-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="83d8e-127">POST /api/TodoItems</span></span> | <span data-ttu-id="83d8e-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-128">Add a new item</span></span> | <span data-ttu-id="83d8e-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-129">To-do item</span></span> | <span data-ttu-id="83d8e-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-130">To-do item</span></span> |
|<span data-ttu-id="83d8e-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="83d8e-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="83d8e-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="83d8e-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="83d8e-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-133">To-do item</span></span> | <span data-ttu-id="83d8e-134">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-134">None</span></span> |
|<span data-ttu-id="83d8e-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="83d8e-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="83d8e-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="83d8e-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="83d8e-137">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-137">None</span></span> | <span data-ttu-id="83d8e-138">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-138">None</span></span>|

<span data-ttu-id="83d8e-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="83d8e-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="83d8e-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="83d8e-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="83d8e-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-151">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="83d8e-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="83d8e-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="83d8e-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="83d8e-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="83d8e-156">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-156">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83d8e-159">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="83d8e-160">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="83d8e-161">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="83d8e-162">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="83d8e-163">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-163">The preceding commands:</span></span>

  * <span data-ttu-id="83d8e-164">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="83d8e-165">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="83d8e-167">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-167">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="83d8e-169">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="83d8e-171">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.0]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="83d8e-172">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="83d8e-174">API のテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-174">Test the API</span></span>

<span data-ttu-id="83d8e-175">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="83d8e-176">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83d8e-178">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="83d8e-179">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="83d8e-180">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="83d8e-181">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83d8e-183">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="83d8e-184">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="83d8e-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="83d8e-186">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="83d8e-187">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="83d8e-188">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="83d8e-189">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="83d8e-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="83d8e-190">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="83d8e-191">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-191">Add a model class</span></span>

<span data-ttu-id="83d8e-192">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="83d8e-193">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-195">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="83d8e-196">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="83d8e-197">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="83d8e-198">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="83d8e-199">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="83d8e-200">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83d8e-202">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="83d8e-203">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="83d8e-205">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-205">Right-click the project.</span></span> <span data-ttu-id="83d8e-206">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="83d8e-207">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-207">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="83d8e-209">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="83d8e-210">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="83d8e-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="83d8e-212">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="83d8e-213">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="83d8e-214">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-214">Add a database context</span></span>

<span data-ttu-id="83d8e-215">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="83d8e-216">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="83d8e-218">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="83d8e-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="83d8e-219">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="83d8e-220">**[プレリリースを含める]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="83d8e-221">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="83d8e-222">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="83d8e-223">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="83d8e-224">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="83d8e-226">TodoContext データベースコンテキストを追加する</span><span class="sxs-lookup"><span data-stu-id="83d8e-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="83d8e-227">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="83d8e-228">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="83d8e-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="83d8e-230">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="83d8e-231">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="83d8e-232">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="83d8e-232">Register the database context</span></span>

<span data-ttu-id="83d8e-233">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="83d8e-234">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="83d8e-235">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="83d8e-236">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-236">The preceding code:</span></span>

* <span data-ttu-id="83d8e-237">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="83d8e-238">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="83d8e-239">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="83d8e-240">コントローラーをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="83d8e-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-242">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="83d8e-243">**[追加]** > **[新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="83d8e-244">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="83d8e-245">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="83d8e-246">**モデル クラス**で **[TodoItem (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="83d8e-247">**データ コンテキスト クラス**で **[TodoContext (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="83d8e-248">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="83d8e-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="83d8e-250">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="83d8e-251">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-251">The preceding commands:</span></span>

* <span data-ttu-id="83d8e-252">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="83d8e-253">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="83d8e-254">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="83d8e-255">生成されたコード:</span><span class="sxs-lookup"><span data-stu-id="83d8e-255">The generated code:</span></span>

* <span data-ttu-id="83d8e-256">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="83d8e-257">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="83d8e-258">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="83d8e-259">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="83d8e-260">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="83d8e-261">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="83d8e-262">PostTodoItem create 作成メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="83d8e-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="83d8e-263">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="83d8e-264">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="83d8e-265">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="83d8e-266"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="83d8e-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="83d8e-267">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="83d8e-268">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="83d8e-269">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="83d8e-270">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="83d8e-271">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="83d8e-272">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="83d8e-273">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="83d8e-274">Postman をインストールする</span><span class="sxs-lookup"><span data-stu-id="83d8e-274">Install Postman</span></span>

<span data-ttu-id="83d8e-275">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="83d8e-276">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="83d8e-277">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-277">Start the web app.</span></span>
* <span data-ttu-id="83d8e-278">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-278">Start Postman.</span></span>
* <span data-ttu-id="83d8e-279">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="83d8e-280">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="83d8e-281">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="83d8e-282">Postman を使用して PostTodoItem をテストする</span><span class="sxs-lookup"><span data-stu-id="83d8e-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="83d8e-283">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-283">Create a new request.</span></span>
* <span data-ttu-id="83d8e-284">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="83d8e-285">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="83d8e-286">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="83d8e-287">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="83d8e-288">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="83d8e-289">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-289">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="83d8e-291">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-291">Test the location header URI</span></span>

* <span data-ttu-id="83d8e-292">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="83d8e-293">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-293">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="83d8e-295">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-295">Set the method to GET.</span></span>
* <span data-ttu-id="83d8e-296">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="83d8e-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="83d8e-297">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="83d8e-298">GET メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="83d8e-298">Examine the GET methods</span></span>

<span data-ttu-id="83d8e-299">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="83d8e-300">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="83d8e-301">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="83d8e-302">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="83d8e-303">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="83d8e-303">Test Get with Postman</span></span>

* <span data-ttu-id="83d8e-304">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-304">Create a new request.</span></span>
* <span data-ttu-id="83d8e-305">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="83d8e-306">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="83d8e-307">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="83d8e-308">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="83d8e-309">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-309">Select **Send**.</span></span>

<span data-ttu-id="83d8e-310">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-310">This app uses an in-memory database.</span></span> <span data-ttu-id="83d8e-311">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="83d8e-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="83d8e-312">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="83d8e-313">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="83d8e-313">Routing and URL paths</span></span>

<span data-ttu-id="83d8e-314">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="83d8e-315">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="83d8e-316">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="83d8e-317">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="83d8e-318">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="83d8e-319">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="83d8e-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="83d8e-320">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="83d8e-321">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="83d8e-321">This sample doesn't use a template.</span></span> <span data-ttu-id="83d8e-322">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="83d8e-323">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="83d8e-324">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="83d8e-325">戻り値</span><span class="sxs-lookup"><span data-stu-id="83d8e-325">Return values</span></span>

<span data-ttu-id="83d8e-326">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="83d8e-327">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="83d8e-328">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="83d8e-329">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="83d8e-330">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="83d8e-331">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="83d8e-332">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="83d8e-333">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="83d8e-334">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="83d8e-335">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="83d8e-335">The PutTodoItem method</span></span>

<span data-ttu-id="83d8e-336">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="83d8e-337">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="83d8e-338">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="83d8e-339">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="83d8e-340">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="83d8e-341">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="83d8e-342">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="83d8e-343">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="83d8e-344">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="83d8e-345">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="83d8e-346">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="83d8e-347">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-347">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="83d8e-349">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="83d8e-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="83d8e-350">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="83d8e-351">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="83d8e-352">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="83d8e-353">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="83d8e-354">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="83d8e-355">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="83d8e-356">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="83d8e-357">jQuery から API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="83d8e-357">Call the API from jQuery</span></span>

<span data-ttu-id="83d8e-358">手順については、「[チュートリアル: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery)」(チュートリアル: jQuery を使用して ASP.NET Core Web API を呼び出す) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83d8e-359">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="83d8e-360">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-360">Create a web API project.</span></span>
> * <span data-ttu-id="83d8e-361">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="83d8e-362">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-362">Add a controller.</span></span>
> * <span data-ttu-id="83d8e-363">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="83d8e-364">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="83d8e-365">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="83d8e-365">Specify return values.</span></span>
> * <span data-ttu-id="83d8e-366">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="83d8e-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="83d8e-367">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="83d8e-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="83d8e-368">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="83d8e-369">概要</span><span class="sxs-lookup"><span data-stu-id="83d8e-369">Overview</span></span>

<span data-ttu-id="83d8e-370">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="83d8e-371">API</span><span class="sxs-lookup"><span data-stu-id="83d8e-371">API</span></span> | <span data-ttu-id="83d8e-372">説明</span><span class="sxs-lookup"><span data-stu-id="83d8e-372">Description</span></span> | <span data-ttu-id="83d8e-373">要求本文</span><span class="sxs-lookup"><span data-stu-id="83d8e-373">Request body</span></span> | <span data-ttu-id="83d8e-374">応答本文</span><span class="sxs-lookup"><span data-stu-id="83d8e-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="83d8e-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="83d8e-375">GET /api/TodoItems</span></span> | <span data-ttu-id="83d8e-376">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-376">Get all to-do items</span></span> | <span data-ttu-id="83d8e-377">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-377">None</span></span> | <span data-ttu-id="83d8e-378">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="83d8e-378">Array of to-do items</span></span>|
|<span data-ttu-id="83d8e-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="83d8e-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="83d8e-380">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-380">Get an item by ID</span></span> | <span data-ttu-id="83d8e-381">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-381">None</span></span> | <span data-ttu-id="83d8e-382">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-382">To-do item</span></span>|
|<span data-ttu-id="83d8e-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="83d8e-383">POST /api/TodoItems</span></span> | <span data-ttu-id="83d8e-384">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-384">Add a new item</span></span> | <span data-ttu-id="83d8e-385">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-385">To-do item</span></span> | <span data-ttu-id="83d8e-386">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-386">To-do item</span></span> |
|<span data-ttu-id="83d8e-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="83d8e-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="83d8e-388">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="83d8e-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="83d8e-389">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="83d8e-389">To-do item</span></span> | <span data-ttu-id="83d8e-390">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-390">None</span></span> |
|<span data-ttu-id="83d8e-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="83d8e-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="83d8e-392">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="83d8e-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="83d8e-393">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-393">None</span></span> | <span data-ttu-id="83d8e-394">なし</span><span class="sxs-lookup"><span data-stu-id="83d8e-394">None</span></span>|

<span data-ttu-id="83d8e-395">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-395">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="83d8e-401">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="83d8e-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-404">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="83d8e-405">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="83d8e-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-407">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="83d8e-408">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="83d8e-409">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="83d8e-410">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="83d8e-411">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="83d8e-412">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-412">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83d8e-415">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="83d8e-416">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="83d8e-417">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="83d8e-418">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="83d8e-419">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-420">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="83d8e-421">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-421">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="83d8e-423">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="83d8e-425">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="83d8e-426">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="83d8e-428">API のテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-428">Test the API</span></span>

<span data-ttu-id="83d8e-429">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-429">The project template creates a `values` API.</span></span> <span data-ttu-id="83d8e-430">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83d8e-432">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="83d8e-433">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="83d8e-434">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="83d8e-435">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83d8e-437">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="83d8e-438">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="83d8e-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="83d8e-440">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="83d8e-441">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="83d8e-442">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="83d8e-443">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="83d8e-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="83d8e-444">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="83d8e-445">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-445">Add a model class</span></span>

<span data-ttu-id="83d8e-446">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="83d8e-447">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-449">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="83d8e-450">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="83d8e-451">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="83d8e-452">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="83d8e-453">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="83d8e-454">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d8e-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d8e-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83d8e-456">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="83d8e-457">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d8e-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="83d8e-459">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-459">Right-click the project.</span></span> <span data-ttu-id="83d8e-460">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="83d8e-461">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-461">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="83d8e-463">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="83d8e-464">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="83d8e-465">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="83d8e-466">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="83d8e-467">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="83d8e-468">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-468">Add a database context</span></span>

<span data-ttu-id="83d8e-469">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="83d8e-470">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-472">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="83d8e-473">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="83d8e-474">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="83d8e-475">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="83d8e-476">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="83d8e-477">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="83d8e-477">Register the database context</span></span>

<span data-ttu-id="83d8e-478">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="83d8e-479">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="83d8e-480">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="83d8e-481">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-481">The preceding code:</span></span>

* <span data-ttu-id="83d8e-482">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="83d8e-483">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="83d8e-484">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="83d8e-485">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d8e-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d8e-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d8e-487">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="83d8e-488">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="83d8e-489">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="83d8e-490">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="83d8e-492">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83d8e-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="83d8e-493">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="83d8e-494">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="83d8e-495">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-495">The preceding code:</span></span>

* <span data-ttu-id="83d8e-496">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="83d8e-497">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="83d8e-498">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="83d8e-499">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="83d8e-500">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="83d8e-501">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="83d8e-502">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="83d8e-503">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="83d8e-504">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="83d8e-505">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="83d8e-506">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-506">Add Get methods</span></span>

<span data-ttu-id="83d8e-507">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="83d8e-508">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="83d8e-509">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-509">Stop the app if it's still running.</span></span> <span data-ttu-id="83d8e-510">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="83d8e-511">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="83d8e-512">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="83d8e-513">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="83d8e-514">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="83d8e-514">Routing and URL paths</span></span>

<span data-ttu-id="83d8e-515">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="83d8e-516">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="83d8e-517">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="83d8e-518">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="83d8e-519">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="83d8e-520">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="83d8e-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="83d8e-521">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="83d8e-522">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="83d8e-522">This sample doesn't use a template.</span></span> <span data-ttu-id="83d8e-523">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="83d8e-524">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="83d8e-525">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="83d8e-526">戻り値</span><span class="sxs-lookup"><span data-stu-id="83d8e-526">Return values</span></span>

<span data-ttu-id="83d8e-527">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="83d8e-528">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="83d8e-529">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="83d8e-530">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="83d8e-531">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="83d8e-532">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="83d8e-533">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="83d8e-534">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="83d8e-535">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-535">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="83d8e-536">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="83d8e-537">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="83d8e-538">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="83d8e-539">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-539">Start the web app.</span></span>
* <span data-ttu-id="83d8e-540">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-540">Start Postman.</span></span>
* <span data-ttu-id="83d8e-541">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-541">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="83d8e-542">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-542">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="83d8e-543">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-543">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="83d8e-544">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-544">Create a new request.</span></span>
  * <span data-ttu-id="83d8e-545">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-545">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="83d8e-546">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-546">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="83d8e-547">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-547">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="83d8e-548">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-548">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="83d8e-549">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-549">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="83d8e-551">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-551">Add a Create method</span></span>

<span data-ttu-id="83d8e-552">次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-552">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="83d8e-553">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="83d8e-553">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="83d8e-554">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-554">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="83d8e-555">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="83d8e-555">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="83d8e-556">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-556">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="83d8e-557">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="83d8e-557">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="83d8e-558">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-558">Adds a `Location` header to the response.</span></span> <span data-ttu-id="83d8e-559">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-559">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="83d8e-560">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-560">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="83d8e-561">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-561">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="83d8e-562">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-562">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="83d8e-563">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-563">Test the PostTodoItem method</span></span>

* <span data-ttu-id="83d8e-564">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-564">Build the project.</span></span>
* <span data-ttu-id="83d8e-565">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-565">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="83d8e-566">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-566">Select the **Body** tab.</span></span>
* <span data-ttu-id="83d8e-567">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-567">Select the **raw** radio button.</span></span>
* <span data-ttu-id="83d8e-568">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-568">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="83d8e-569">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-569">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="83d8e-570">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-570">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="83d8e-572">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-572">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="83d8e-573">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-573">Test the location header URI</span></span>

* <span data-ttu-id="83d8e-574">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-574">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="83d8e-575">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="83d8e-575">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="83d8e-577">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-577">Set the method to GET.</span></span>
* <span data-ttu-id="83d8e-578">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="83d8e-578">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="83d8e-579">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-579">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="83d8e-580">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-580">Add a PutTodoItem method</span></span>

<span data-ttu-id="83d8e-581">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-581">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="83d8e-582">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-582">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="83d8e-583">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-583">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="83d8e-584">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-584">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="83d8e-585">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-585">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="83d8e-586">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-586">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="83d8e-587">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-587">Test the PutTodoItem method</span></span>

<span data-ttu-id="83d8e-588">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-588">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="83d8e-589">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-589">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="83d8e-590">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-590">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="83d8e-591">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-591">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="83d8e-592">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-592">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="83d8e-594">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-594">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="83d8e-595">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-595">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="83d8e-596">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-596">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="83d8e-597">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="83d8e-597">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="83d8e-598">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-598">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="83d8e-599">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-599">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="83d8e-600">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-600">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="83d8e-601">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-601">Select **Send**</span></span>

<span data-ttu-id="83d8e-602">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-602">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="83d8e-603">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-603">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="83d8e-604">jQuery での API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="83d8e-604">Call the API with jQuery</span></span>

<span data-ttu-id="83d8e-605">このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-605">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="83d8e-606">jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-606">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="83d8e-607">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-607">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="83d8e-608">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-608">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="83d8e-609">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-609">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="83d8e-610">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-610">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="83d8e-611">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-611">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="83d8e-612">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-612">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="83d8e-613">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-613">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="83d8e-614">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-614">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="83d8e-615">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-615">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="83d8e-616">jQuery を取得するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-616">There are several ways to get jQuery.</span></span> <span data-ttu-id="83d8e-617">前のスニペットでは、ライブラリは CDN から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-617">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="83d8e-618">このサンプルでは、API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-618">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="83d8e-619">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="83d8e-620">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="83d8e-620">Get a list of to-do items</span></span>

<span data-ttu-id="83d8e-621">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-621">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="83d8e-622">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="83d8e-623">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="83d8e-624">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="83d8e-624">Add a to-do item</span></span>

<span data-ttu-id="83d8e-625">[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-625">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="83d8e-626">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="83d8e-627">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="83d8e-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="83d8e-628">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="83d8e-629">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="83d8e-629">Update a to-do item</span></span>

<span data-ttu-id="83d8e-630">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="83d8e-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="83d8e-631">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="83d8e-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="83d8e-632">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="83d8e-632">Delete a to-do item</span></span>

<span data-ttu-id="83d8e-633">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="83d8e-634">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="83d8e-634">Additional resources</span></span>

<span data-ttu-id="83d8e-635">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="83d8e-635">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="83d8e-636">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-636">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="83d8e-637">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="83d8e-637">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="83d8e-638">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="83d8e-638">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
