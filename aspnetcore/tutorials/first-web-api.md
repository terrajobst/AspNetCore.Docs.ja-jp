---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 855d05fa2b9c1a7572212c40adbe61bb396f4bac
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819837"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="b6ed3-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="b6ed3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="b6ed3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="b6ed3-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6ed3-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b6ed3-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-107">Create a web API project.</span></span>
> * <span data-ttu-id="b6ed3-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b6ed3-109">CRUD メソッドを使用してコントローラーをスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b6ed3-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b6ed3-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-111">Call the web API with Postman.</span></span>

<span data-ttu-id="b6ed3-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b6ed3-113">概要</span><span class="sxs-lookup"><span data-stu-id="b6ed3-113">Overview</span></span>

<span data-ttu-id="b6ed3-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b6ed3-115">API</span><span class="sxs-lookup"><span data-stu-id="b6ed3-115">API</span></span> | <span data-ttu-id="b6ed3-116">説明</span><span class="sxs-lookup"><span data-stu-id="b6ed3-116">Description</span></span> | <span data-ttu-id="b6ed3-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="b6ed3-117">Request body</span></span> | <span data-ttu-id="b6ed3-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="b6ed3-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="b6ed3-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b6ed3-119">GET /api/TodoItems</span></span> | <span data-ttu-id="b6ed3-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-120">Get all to-do items</span></span> | <span data-ttu-id="b6ed3-121">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-121">None</span></span> | <span data-ttu-id="b6ed3-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="b6ed3-122">Array of to-do items</span></span>|
|<span data-ttu-id="b6ed3-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b6ed3-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="b6ed3-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-124">Get an item by ID</span></span> | <span data-ttu-id="b6ed3-125">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-125">None</span></span> | <span data-ttu-id="b6ed3-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-126">To-do item</span></span>|
|<span data-ttu-id="b6ed3-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b6ed3-127">POST /api/TodoItems</span></span> | <span data-ttu-id="b6ed3-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-128">Add a new item</span></span> | <span data-ttu-id="b6ed3-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-129">To-do item</span></span> | <span data-ttu-id="b6ed3-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-130">To-do item</span></span> |
|<span data-ttu-id="b6ed3-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b6ed3-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="b6ed3-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b6ed3-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b6ed3-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-133">To-do item</span></span> | <span data-ttu-id="b6ed3-134">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-134">None</span></span> |
|<span data-ttu-id="b6ed3-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b6ed3-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="b6ed3-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b6ed3-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b6ed3-137">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-137">None</span></span> | <span data-ttu-id="b6ed3-138">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-138">None</span></span>|

<span data-ttu-id="b6ed3-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b6ed3-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b6ed3-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b6ed3-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-151">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b6ed3-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b6ed3-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b6ed3-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="b6ed3-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="b6ed3-156">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-156">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ed3-159">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b6ed3-160">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b6ed3-161">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="b6ed3-162">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b6ed3-163">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-163">The preceding commands:</span></span>

  * <span data-ttu-id="b6ed3-164">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b6ed3-165">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b6ed3-167">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-167">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b6ed3-169">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="b6ed3-171">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.0]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="b6ed3-172">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="b6ed3-174">API のテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-174">Test the API</span></span>

<span data-ttu-id="b6ed3-175">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="b6ed3-176">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ed3-178">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b6ed3-179">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b6ed3-180">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b6ed3-181">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ed3-183">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b6ed3-184">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b6ed3-186">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b6ed3-187">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b6ed3-188">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b6ed3-189">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="b6ed3-190">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="b6ed3-191">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-191">Add a model class</span></span>

<span data-ttu-id="b6ed3-192">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b6ed3-193">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-195">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b6ed3-196">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b6ed3-197">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="b6ed3-198">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b6ed3-199">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b6ed3-200">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ed3-202">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b6ed3-203">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b6ed3-205">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-205">Right-click the project.</span></span> <span data-ttu-id="b6ed3-206">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b6ed3-207">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-207">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b6ed3-209">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b6ed3-210">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b6ed3-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="b6ed3-212">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b6ed3-213">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b6ed3-214">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-214">Add a database context</span></span>

<span data-ttu-id="b6ed3-215">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b6ed3-216">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="b6ed3-218">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="b6ed3-219">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b6ed3-220">**[プレリリースを含める]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="b6ed3-221">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="b6ed3-222">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="b6ed3-223">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="b6ed3-224">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b6ed3-226">TodoContext データベースコンテキストを追加する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="b6ed3-227">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b6ed3-228">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b6ed3-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b6ed3-230">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b6ed3-231">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b6ed3-232">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="b6ed3-232">Register the database context</span></span>

<span data-ttu-id="b6ed3-233">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b6ed3-234">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="b6ed3-235">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b6ed3-236">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-236">The preceding code:</span></span>

* <span data-ttu-id="b6ed3-237">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b6ed3-238">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b6ed3-239">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b6ed3-240">コントローラーをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="b6ed3-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-242">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b6ed3-243">**[追加]** > **[新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b6ed3-244">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="b6ed3-245">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b6ed3-246">**モデル クラス**で **[TodoItem (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b6ed3-247">**データ コンテキスト クラス**で **[TodoContext (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b6ed3-248">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b6ed3-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b6ed3-250">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="b6ed3-251">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-251">The preceding commands:</span></span>

* <span data-ttu-id="b6ed3-252">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b6ed3-253">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b6ed3-254">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b6ed3-255">生成されたコード:</span><span class="sxs-lookup"><span data-stu-id="b6ed3-255">The generated code:</span></span>

* <span data-ttu-id="b6ed3-256">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="b6ed3-257">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="b6ed3-258">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b6ed3-259">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b6ed3-260">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b6ed3-261">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="b6ed3-262">PostTodoItem create 作成メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="b6ed3-263">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b6ed3-264">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="b6ed3-265">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b6ed3-266"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="b6ed3-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b6ed3-267">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="b6ed3-268">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b6ed3-269">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b6ed3-270">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b6ed3-271">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b6ed3-272">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b6ed3-273">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b6ed3-274">Postman をインストールする</span><span class="sxs-lookup"><span data-stu-id="b6ed3-274">Install Postman</span></span>

<span data-ttu-id="b6ed3-275">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b6ed3-276">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b6ed3-277">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-277">Start the web app.</span></span>
* <span data-ttu-id="b6ed3-278">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-278">Start Postman.</span></span>
* <span data-ttu-id="b6ed3-279">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="b6ed3-280">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b6ed3-281">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b6ed3-282">Postman を使用して PostTodoItem をテストする</span><span class="sxs-lookup"><span data-stu-id="b6ed3-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b6ed3-283">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-283">Create a new request.</span></span>
* <span data-ttu-id="b6ed3-284">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b6ed3-285">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="b6ed3-286">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b6ed3-287">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b6ed3-288">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b6ed3-289">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-289">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b6ed3-291">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-291">Test the location header URI</span></span>

* <span data-ttu-id="b6ed3-292">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b6ed3-293">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-293">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="b6ed3-295">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-295">Set the method to GET.</span></span>
* <span data-ttu-id="b6ed3-296">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="b6ed3-297">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b6ed3-298">GET メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-298">Examine the GET methods</span></span>

<span data-ttu-id="b6ed3-299">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b6ed3-300">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b6ed3-301">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="b6ed3-302">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b6ed3-303">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="b6ed3-303">Test Get with Postman</span></span>

* <span data-ttu-id="b6ed3-304">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-304">Create a new request.</span></span>
* <span data-ttu-id="b6ed3-305">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b6ed3-306">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b6ed3-307">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b6ed3-308">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b6ed3-309">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-309">Select **Send**.</span></span>

<span data-ttu-id="b6ed3-310">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-310">This app uses an in-memory database.</span></span> <span data-ttu-id="b6ed3-311">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b6ed3-312">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b6ed3-313">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="b6ed3-313">Routing and URL paths</span></span>

<span data-ttu-id="b6ed3-314">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b6ed3-315">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b6ed3-316">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b6ed3-317">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b6ed3-318">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b6ed3-319">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b6ed3-320">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b6ed3-321">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-321">This sample doesn't use a template.</span></span> <span data-ttu-id="b6ed3-322">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b6ed3-323">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b6ed3-324">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b6ed3-325">戻り値</span><span class="sxs-lookup"><span data-stu-id="b6ed3-325">Return values</span></span>

<span data-ttu-id="b6ed3-326">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b6ed3-327">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b6ed3-328">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b6ed3-329">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b6ed3-330">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b6ed3-331">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b6ed3-332">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="b6ed3-333">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b6ed3-334">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b6ed3-335">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="b6ed3-335">The PutTodoItem method</span></span>

<span data-ttu-id="b6ed3-336">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b6ed3-337">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b6ed3-338">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b6ed3-339">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b6ed3-340">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b6ed3-341">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b6ed3-342">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="b6ed3-343">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="b6ed3-344">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b6ed3-345">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b6ed3-346">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b6ed3-347">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-347">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b6ed3-349">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="b6ed3-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="b6ed3-350">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="b6ed3-351">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b6ed3-352">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b6ed3-353">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b6ed3-354">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b6ed3-355">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="b6ed3-356">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="b6ed3-357">jQuery から API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="b6ed3-357">Call the API from jQuery</span></span>

<span data-ttu-id="b6ed3-358">手順については、「[チュートリアル: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery)」(チュートリアル: jQuery を使用して ASP.NET Core Web API を呼び出す) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b6ed3-359">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b6ed3-360">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-360">Create a web API project.</span></span>
> * <span data-ttu-id="b6ed3-361">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b6ed3-362">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-362">Add a controller.</span></span>
> * <span data-ttu-id="b6ed3-363">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="b6ed3-364">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="b6ed3-365">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-365">Specify return values.</span></span>
> * <span data-ttu-id="b6ed3-366">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="b6ed3-367">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="b6ed3-368">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="b6ed3-369">概要</span><span class="sxs-lookup"><span data-stu-id="b6ed3-369">Overview</span></span>

<span data-ttu-id="b6ed3-370">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b6ed3-371">API</span><span class="sxs-lookup"><span data-stu-id="b6ed3-371">API</span></span> | <span data-ttu-id="b6ed3-372">説明</span><span class="sxs-lookup"><span data-stu-id="b6ed3-372">Description</span></span> | <span data-ttu-id="b6ed3-373">要求本文</span><span class="sxs-lookup"><span data-stu-id="b6ed3-373">Request body</span></span> | <span data-ttu-id="b6ed3-374">応答本文</span><span class="sxs-lookup"><span data-stu-id="b6ed3-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="b6ed3-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b6ed3-375">GET /api/TodoItems</span></span> | <span data-ttu-id="b6ed3-376">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-376">Get all to-do items</span></span> | <span data-ttu-id="b6ed3-377">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-377">None</span></span> | <span data-ttu-id="b6ed3-378">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="b6ed3-378">Array of to-do items</span></span>|
|<span data-ttu-id="b6ed3-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b6ed3-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="b6ed3-380">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-380">Get an item by ID</span></span> | <span data-ttu-id="b6ed3-381">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-381">None</span></span> | <span data-ttu-id="b6ed3-382">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-382">To-do item</span></span>|
|<span data-ttu-id="b6ed3-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b6ed3-383">POST /api/TodoItems</span></span> | <span data-ttu-id="b6ed3-384">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-384">Add a new item</span></span> | <span data-ttu-id="b6ed3-385">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-385">To-do item</span></span> | <span data-ttu-id="b6ed3-386">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-386">To-do item</span></span> |
|<span data-ttu-id="b6ed3-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b6ed3-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="b6ed3-388">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b6ed3-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b6ed3-389">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="b6ed3-389">To-do item</span></span> | <span data-ttu-id="b6ed3-390">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-390">None</span></span> |
|<span data-ttu-id="b6ed3-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b6ed3-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="b6ed3-392">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b6ed3-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b6ed3-393">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-393">None</span></span> | <span data-ttu-id="b6ed3-394">なし</span><span class="sxs-lookup"><span data-stu-id="b6ed3-394">None</span></span>|

<span data-ttu-id="b6ed3-395">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-395">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b6ed3-401">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b6ed3-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-404">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b6ed3-405">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b6ed3-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-407">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b6ed3-408">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b6ed3-409">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b6ed3-410">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="b6ed3-411">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="b6ed3-412">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-412">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ed3-415">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b6ed3-416">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b6ed3-417">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="b6ed3-418">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="b6ed3-419">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-420">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b6ed3-421">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-421">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b6ed3-423">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="b6ed3-425">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="b6ed3-426">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="b6ed3-428">API のテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-428">Test the API</span></span>

<span data-ttu-id="b6ed3-429">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-429">The project template creates a `values` API.</span></span> <span data-ttu-id="b6ed3-430">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ed3-432">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b6ed3-433">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b6ed3-434">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b6ed3-435">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ed3-437">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b6ed3-438">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b6ed3-440">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b6ed3-441">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b6ed3-442">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b6ed3-443">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="b6ed3-444">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="b6ed3-445">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-445">Add a model class</span></span>

<span data-ttu-id="b6ed3-446">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b6ed3-447">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-449">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b6ed3-450">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b6ed3-451">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="b6ed3-452">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b6ed3-453">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b6ed3-454">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b6ed3-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ed3-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ed3-456">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b6ed3-457">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b6ed3-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b6ed3-459">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-459">Right-click the project.</span></span> <span data-ttu-id="b6ed3-460">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b6ed3-461">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-461">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b6ed3-463">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b6ed3-464">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b6ed3-465">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="b6ed3-466">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b6ed3-467">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b6ed3-468">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-468">Add a database context</span></span>

<span data-ttu-id="b6ed3-469">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b6ed3-470">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-472">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b6ed3-473">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b6ed3-474">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b6ed3-475">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b6ed3-476">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b6ed3-477">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="b6ed3-477">Register the database context</span></span>

<span data-ttu-id="b6ed3-478">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b6ed3-479">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="b6ed3-480">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="b6ed3-481">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-481">The preceding code:</span></span>

* <span data-ttu-id="b6ed3-482">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b6ed3-483">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b6ed3-484">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b6ed3-485">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-487">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b6ed3-488">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="b6ed3-489">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="b6ed3-490">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b6ed3-492">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b6ed3-493">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="b6ed3-494">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="b6ed3-495">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-495">The preceding code:</span></span>

* <span data-ttu-id="b6ed3-496">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="b6ed3-497">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="b6ed3-498">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b6ed3-499">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b6ed3-500">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b6ed3-501">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="b6ed3-502">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="b6ed3-503">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="b6ed3-504">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="b6ed3-505">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="b6ed3-506">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-506">Add Get methods</span></span>

<span data-ttu-id="b6ed3-507">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="b6ed3-508">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="b6ed3-509">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-509">Stop the app if it's still running.</span></span> <span data-ttu-id="b6ed3-510">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="b6ed3-511">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="b6ed3-512">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="b6ed3-513">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="b6ed3-514">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="b6ed3-514">Routing and URL paths</span></span>

<span data-ttu-id="b6ed3-515">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b6ed3-516">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b6ed3-517">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="b6ed3-518">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b6ed3-519">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="b6ed3-520">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b6ed3-521">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b6ed3-522">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-522">This sample doesn't use a template.</span></span> <span data-ttu-id="b6ed3-523">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b6ed3-524">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b6ed3-525">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b6ed3-526">戻り値</span><span class="sxs-lookup"><span data-stu-id="b6ed3-526">Return values</span></span>

<span data-ttu-id="b6ed3-527">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b6ed3-528">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b6ed3-529">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b6ed3-530">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b6ed3-531">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b6ed3-532">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b6ed3-533">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="b6ed3-534">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b6ed3-535">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-535">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="b6ed3-536">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="b6ed3-537">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b6ed3-538">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b6ed3-539">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-539">Start the web app.</span></span>
* <span data-ttu-id="b6ed3-540">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-540">Start Postman.</span></span>
* <span data-ttu-id="b6ed3-541">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-541">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6ed3-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ed3-542">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ed3-543">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-543">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b6ed3-544">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b6ed3-544">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b6ed3-545">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-545">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="b6ed3-546">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-546">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="b6ed3-547">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-547">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="b6ed3-548">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-548">Create a new request.</span></span>
  * <span data-ttu-id="b6ed3-549">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-549">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="b6ed3-550">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-550">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="b6ed3-551">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-551">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="b6ed3-552">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-552">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b6ed3-553">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-553">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="b6ed3-555">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-555">Add a Create method</span></span>

<span data-ttu-id="b6ed3-556">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-556">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="b6ed3-557">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-557">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="b6ed3-558">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b6ed3-559">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="b6ed3-559">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="b6ed3-560">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-560">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="b6ed3-561">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b6ed3-562">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-562">Adds a `Location` header to the response.</span></span> <span data-ttu-id="b6ed3-563">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-563">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="b6ed3-564">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b6ed3-565">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b6ed3-566">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="b6ed3-567">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-567">Test the PostTodoItem method</span></span>

* <span data-ttu-id="b6ed3-568">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-568">Build the project.</span></span>
* <span data-ttu-id="b6ed3-569">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-569">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b6ed3-570">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-570">Select the **Body** tab.</span></span>
* <span data-ttu-id="b6ed3-571">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-571">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b6ed3-572">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-572">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b6ed3-573">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-573">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b6ed3-574">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-574">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="b6ed3-576">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-576">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b6ed3-577">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-577">Test the location header URI</span></span>

* <span data-ttu-id="b6ed3-578">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-578">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b6ed3-579">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-579">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="b6ed3-581">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-581">Set the method to GET.</span></span>
* <span data-ttu-id="b6ed3-582">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-582">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="b6ed3-583">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-583">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="b6ed3-584">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-584">Add a PutTodoItem method</span></span>

<span data-ttu-id="b6ed3-585">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-585">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="b6ed3-586">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-586">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b6ed3-587">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-587">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b6ed3-588">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-588">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b6ed3-589">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-589">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b6ed3-590">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-590">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b6ed3-591">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-591">Test the PutTodoItem method</span></span>

<span data-ttu-id="b6ed3-592">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-592">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="b6ed3-593">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-593">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b6ed3-594">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-594">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b6ed3-595">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-595">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b6ed3-596">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-596">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="b6ed3-598">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-598">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="b6ed3-599">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-599">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="b6ed3-600">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-600">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b6ed3-601">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="b6ed3-601">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b6ed3-602">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-602">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b6ed3-603">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-603">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b6ed3-604">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-604">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="b6ed3-605">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-605">Select **Send**</span></span>

<span data-ttu-id="b6ed3-606">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-606">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="b6ed3-607">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-607">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="b6ed3-608">jQuery での API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="b6ed3-608">Call the API with jQuery</span></span>

<span data-ttu-id="b6ed3-609">このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-609">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="b6ed3-610">jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-610">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="b6ed3-611">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="b6ed3-612">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="b6ed3-613">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="b6ed3-614">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="b6ed3-615">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="b6ed3-616">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="b6ed3-617">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="b6ed3-618">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="b6ed3-619">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="b6ed3-620">jQuery を取得するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-620">There are several ways to get jQuery.</span></span> <span data-ttu-id="b6ed3-621">前のスニペットでは、ライブラリは CDN から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-621">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="b6ed3-622">このサンプルでは、API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-622">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="b6ed3-623">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="b6ed3-624">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="b6ed3-624">Get a list of to-do items</span></span>

<span data-ttu-id="b6ed3-625">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-625">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="b6ed3-626">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="b6ed3-627">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="b6ed3-628">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="b6ed3-628">Add a to-do item</span></span>

<span data-ttu-id="b6ed3-629">[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-629">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="b6ed3-630">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="b6ed3-631">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="b6ed3-632">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="b6ed3-633">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="b6ed3-633">Update a to-do item</span></span>

<span data-ttu-id="b6ed3-634">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="b6ed3-635">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="b6ed3-636">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="b6ed3-636">Delete a to-do item</span></span>

<span data-ttu-id="b6ed3-637">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b6ed3-638">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b6ed3-638">Additional resources</span></span>

<span data-ttu-id="b6ed3-639">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="b6ed3-640">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b6ed3-641">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6ed3-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="b6ed3-642">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="b6ed3-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
