---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 99985e9fb1134c2ba808434f8d24c4a768773268
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022594"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="9b666-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="9b666-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="9b666-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="9b666-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="9b666-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b666-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b666-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b666-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9b666-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="9b666-107">Create a web API project.</span></span>
> * <span data-ttu-id="9b666-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="9b666-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9b666-109">CRUD メソッドを使用してコントローラーをスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="9b666-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="9b666-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="9b666-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="9b666-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="9b666-111">Call the web API with Postman.</span></span>

<span data-ttu-id="9b666-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="9b666-113">概要</span><span class="sxs-lookup"><span data-stu-id="9b666-113">Overview</span></span>

<span data-ttu-id="9b666-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9b666-115">API</span><span class="sxs-lookup"><span data-stu-id="9b666-115">API</span></span> | <span data-ttu-id="9b666-116">説明</span><span class="sxs-lookup"><span data-stu-id="9b666-116">Description</span></span> | <span data-ttu-id="9b666-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="9b666-117">Request body</span></span> | <span data-ttu-id="9b666-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="9b666-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="9b666-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9b666-119">GET /api/TodoItems</span></span> | <span data-ttu-id="9b666-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="9b666-120">Get all to-do items</span></span> | <span data-ttu-id="9b666-121">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-121">None</span></span> | <span data-ttu-id="9b666-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="9b666-122">Array of to-do items</span></span>|
|<span data-ttu-id="9b666-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9b666-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="9b666-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="9b666-124">Get an item by ID</span></span> | <span data-ttu-id="9b666-125">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-125">None</span></span> | <span data-ttu-id="9b666-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-126">To-do item</span></span>|
|<span data-ttu-id="9b666-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9b666-127">POST /api/TodoItems</span></span> | <span data-ttu-id="9b666-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-128">Add a new item</span></span> | <span data-ttu-id="9b666-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-129">To-do item</span></span> | <span data-ttu-id="9b666-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-130">To-do item</span></span> |
|<span data-ttu-id="9b666-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9b666-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="9b666-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="9b666-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9b666-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-133">To-do item</span></span> | <span data-ttu-id="9b666-134">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-134">None</span></span> |
|<span data-ttu-id="9b666-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9b666-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="9b666-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9b666-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9b666-137">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-137">None</span></span> | <span data-ttu-id="9b666-138">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-138">None</span></span>|

<span data-ttu-id="9b666-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="9b666-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9b666-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9b666-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9b666-149">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="9b666-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-151">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9b666-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9b666-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9b666-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b666-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="9b666-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="9b666-156">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="9b666-156">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9b666-159">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="9b666-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9b666-160">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="9b666-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9b666-161">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="9b666-162">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="9b666-163">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9b666-163">The preceding commands:</span></span>

  * <span data-ttu-id="9b666-164">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="9b666-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="9b666-165">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9b666-167">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-167">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9b666-169">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="9b666-171">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.0]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="9b666-172">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="9b666-174">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```console
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="9b666-175">API のテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-175">Test the API</span></span>

<span data-ttu-id="9b666-176">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="9b666-177">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9b666-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b666-179">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9b666-180">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="9b666-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9b666-181">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9b666-182">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9b666-184">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9b666-185">ブラウザーで、次の URL に移動します: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="9b666-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9b666-187">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9b666-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9b666-188">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="9b666-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9b666-189">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9b666-190">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="9b666-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="9b666-191">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-191">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="9b666-192">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-192">Add a model class</span></span>

<span data-ttu-id="9b666-193">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="9b666-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9b666-194">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="9b666-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-196">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9b666-197">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9b666-198">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9b666-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="9b666-199">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9b666-200">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9b666-201">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9b666-203">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9b666-204">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9b666-206">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-206">Right-click the project.</span></span> <span data-ttu-id="9b666-207">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9b666-208">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9b666-208">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9b666-210">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9b666-211">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9b666-212">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="9b666-213">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="9b666-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9b666-214">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9b666-215">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-215">Add a database context</span></span>

<span data-ttu-id="9b666-216">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="9b666-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9b666-217">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="9b666-219">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="9b666-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="9b666-220">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="9b666-221">**[プレリリースを含める]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9b666-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="9b666-222">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="9b666-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="9b666-223">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="9b666-224">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="9b666-225">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="9b666-227">TodoContext データベースコンテキストを追加する</span><span class="sxs-lookup"><span data-stu-id="9b666-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="9b666-228">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9b666-229">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9b666-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9b666-231">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9b666-232">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="9b666-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9b666-233">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="9b666-233">Register the database context</span></span>

<span data-ttu-id="9b666-234">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9b666-235">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9b666-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="9b666-236">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="9b666-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="9b666-237">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9b666-237">The preceding code:</span></span>

* <span data-ttu-id="9b666-238">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="9b666-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9b666-239">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9b666-240">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="9b666-241">コントローラーをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="9b666-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-243">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9b666-244">**[追加]** > **[新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9b666-245">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="9b666-246">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="9b666-247">**モデル クラス**で **[TodoItem (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="9b666-248">**データ コンテキスト クラス**で **[TodoContext (TodoAPI.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="9b666-249">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9b666-250">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9b666-251">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-251">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="9b666-252">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9b666-252">The preceding commands:</span></span>

* <span data-ttu-id="9b666-253">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="9b666-254">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9b666-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="9b666-255">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="9b666-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="9b666-256">生成されたコード:</span><span class="sxs-lookup"><span data-stu-id="9b666-256">The generated code:</span></span>

* <span data-ttu-id="9b666-257">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="9b666-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="9b666-258">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="9b666-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="9b666-259">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="9b666-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9b666-260">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9b666-261">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="9b666-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9b666-262">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="9b666-263">PostTodoItem create 作成メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="9b666-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="9b666-264">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="9b666-265">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="9b666-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="9b666-266">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="9b666-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9b666-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="9b666-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="9b666-268">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="9b666-269">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="9b666-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9b666-270">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="9b666-271">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="9b666-272">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9b666-273">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9b666-274">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="9b666-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="9b666-275">Postman をインストールする</span><span class="sxs-lookup"><span data-stu-id="9b666-275">Install Postman</span></span>

<span data-ttu-id="9b666-276">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="9b666-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9b666-277">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9b666-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9b666-278">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9b666-278">Start the web app.</span></span>
* <span data-ttu-id="9b666-279">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="9b666-279">Start Postman.</span></span>
* <span data-ttu-id="9b666-280">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="9b666-281">**[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="9b666-282">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="9b666-283">Postman を使用して PostTodoItem をテストする</span><span class="sxs-lookup"><span data-stu-id="9b666-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="9b666-284">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-284">Create a new request.</span></span>
* <span data-ttu-id="9b666-285">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9b666-286">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="9b666-287">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9b666-288">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9b666-289">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="9b666-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9b666-290">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-290">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="9b666-292">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-292">Test the location header URI</span></span>

* <span data-ttu-id="9b666-293">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9b666-294">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="9b666-294">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="9b666-296">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-296">Set the method to GET.</span></span>
* <span data-ttu-id="9b666-297">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="9b666-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="9b666-298">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="9b666-299">GET メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="9b666-299">Examine the GET methods</span></span>

<span data-ttu-id="9b666-300">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="9b666-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="9b666-301">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9b666-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="9b666-302">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9b666-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="9b666-303">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="9b666-304">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="9b666-304">Test Get with Postman</span></span>

* <span data-ttu-id="9b666-305">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-305">Create a new request.</span></span>
* <span data-ttu-id="9b666-306">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="9b666-307">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9b666-308">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b666-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9b666-309">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9b666-310">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-310">Select **Send**.</span></span>

<span data-ttu-id="9b666-311">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-311">This app uses an in-memory database.</span></span> <span data-ttu-id="9b666-312">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="9b666-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="9b666-313">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="9b666-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="9b666-314">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="9b666-314">Routing and URL paths</span></span>

<span data-ttu-id="9b666-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="9b666-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9b666-316">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9b666-317">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b666-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="9b666-318">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9b666-319">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="9b666-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="9b666-320">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="9b666-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9b666-321">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9b666-322">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="9b666-322">This sample doesn't use a template.</span></span> <span data-ttu-id="9b666-323">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9b666-324">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="9b666-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9b666-325">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9b666-326">戻り値</span><span class="sxs-lookup"><span data-stu-id="9b666-326">Return values</span></span>

<span data-ttu-id="9b666-327">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="9b666-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9b666-328">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="9b666-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9b666-329">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9b666-330">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9b666-331">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="9b666-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9b666-332">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9b666-333">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="9b666-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="9b666-334">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="9b666-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9b666-335">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="9b666-336">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="9b666-336">The PutTodoItem method</span></span>

<span data-ttu-id="9b666-337">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="9b666-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="9b666-338">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b666-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9b666-339">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="9b666-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9b666-340">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="9b666-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9b666-341">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b666-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9b666-342">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9b666-343">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="9b666-344">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="9b666-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="9b666-345">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9b666-346">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b666-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9b666-347">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9b666-348">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="9b666-348">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="9b666-350">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="9b666-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="9b666-351">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="9b666-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="9b666-352">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="9b666-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9b666-353">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9b666-354">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b666-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9b666-355">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9b666-356">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="9b666-357">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-357">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="9b666-358">jQuery から API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="9b666-358">Call the API from jQuery</span></span>

<span data-ttu-id="9b666-359">手順については、「[チュートリアル: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery)」(チュートリアル: jQuery を使用して ASP.NET Core Web API を呼び出す) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-359">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b666-360">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b666-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9b666-361">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="9b666-361">Create a web API project.</span></span>
> * <span data-ttu-id="9b666-362">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="9b666-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9b666-363">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="9b666-363">Add a controller.</span></span>
> * <span data-ttu-id="9b666-364">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="9b666-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="9b666-365">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="9b666-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="9b666-366">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="9b666-366">Specify return values.</span></span>
> * <span data-ttu-id="9b666-367">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="9b666-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="9b666-368">jQuery で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="9b666-368">Call the web API with jQuery.</span></span>

<span data-ttu-id="9b666-369">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="9b666-370">概要</span><span class="sxs-lookup"><span data-stu-id="9b666-370">Overview</span></span>

<span data-ttu-id="9b666-371">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9b666-372">API</span><span class="sxs-lookup"><span data-stu-id="9b666-372">API</span></span> | <span data-ttu-id="9b666-373">説明</span><span class="sxs-lookup"><span data-stu-id="9b666-373">Description</span></span> | <span data-ttu-id="9b666-374">要求本文</span><span class="sxs-lookup"><span data-stu-id="9b666-374">Request body</span></span> | <span data-ttu-id="9b666-375">応答本文</span><span class="sxs-lookup"><span data-stu-id="9b666-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="9b666-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9b666-376">GET /api/TodoItems</span></span> | <span data-ttu-id="9b666-377">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="9b666-377">Get all to-do items</span></span> | <span data-ttu-id="9b666-378">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-378">None</span></span> | <span data-ttu-id="9b666-379">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="9b666-379">Array of to-do items</span></span>|
|<span data-ttu-id="9b666-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9b666-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="9b666-381">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="9b666-381">Get an item by ID</span></span> | <span data-ttu-id="9b666-382">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-382">None</span></span> | <span data-ttu-id="9b666-383">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-383">To-do item</span></span>|
|<span data-ttu-id="9b666-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9b666-384">POST /api/TodoItems</span></span> | <span data-ttu-id="9b666-385">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-385">Add a new item</span></span> | <span data-ttu-id="9b666-386">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-386">To-do item</span></span> | <span data-ttu-id="9b666-387">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-387">To-do item</span></span> |
|<span data-ttu-id="9b666-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9b666-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="9b666-389">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="9b666-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9b666-390">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="9b666-390">To-do item</span></span> | <span data-ttu-id="9b666-391">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-391">None</span></span> |
|<span data-ttu-id="9b666-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9b666-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="9b666-393">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9b666-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9b666-394">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-394">None</span></span> | <span data-ttu-id="9b666-395">なし</span><span class="sxs-lookup"><span data-stu-id="9b666-395">None</span></span>|

<span data-ttu-id="9b666-396">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="9b666-396">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9b666-402">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9b666-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-405">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9b666-406">Web プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="9b666-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-408">**[ファイル]** メニューで **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9b666-409">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9b666-410">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9b666-411">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b666-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="9b666-412">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="9b666-413">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="9b666-413">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9b666-416">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="9b666-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9b666-417">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="9b666-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9b666-418">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-418">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="9b666-419">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="9b666-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="9b666-420">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-421">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9b666-422">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-422">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9b666-424">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="9b666-426">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9b666-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="9b666-427">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="9b666-429">API のテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-429">Test the API</span></span>

<span data-ttu-id="9b666-430">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-430">The project template creates a `values` API.</span></span> <span data-ttu-id="9b666-431">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9b666-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b666-433">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9b666-434">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="9b666-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9b666-435">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9b666-436">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9b666-438">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b666-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9b666-439">ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="9b666-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9b666-441">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9b666-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9b666-442">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="9b666-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9b666-443">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9b666-444">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="9b666-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="9b666-445">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="9b666-446">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-446">Add a model class</span></span>

<span data-ttu-id="9b666-447">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="9b666-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9b666-448">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="9b666-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-450">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9b666-451">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9b666-452">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9b666-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="9b666-453">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9b666-454">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9b666-455">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b666-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b666-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9b666-457">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9b666-458">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b666-459">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9b666-460">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-460">Right-click the project.</span></span> <span data-ttu-id="9b666-461">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9b666-462">フォルダーに *Models* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9b666-462">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9b666-464">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > \**[Empty Class]\(空のクラス)\** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9b666-465">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9b666-466">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="9b666-467">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="9b666-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9b666-468">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9b666-469">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-469">Add a database context</span></span>

<span data-ttu-id="9b666-470">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="9b666-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9b666-471">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-473">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9b666-474">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9b666-475">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9b666-476">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9b666-477">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9b666-478">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="9b666-478">Register the database context</span></span>

<span data-ttu-id="9b666-479">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9b666-480">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9b666-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="9b666-481">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="9b666-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="9b666-482">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9b666-482">The preceding code:</span></span>

* <span data-ttu-id="9b666-483">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="9b666-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9b666-484">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9b666-485">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9b666-486">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-488">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9b666-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9b666-489">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="9b666-490">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="9b666-491">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9b666-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9b666-494">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="9b666-495">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="9b666-496">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="9b666-496">The preceding code:</span></span>

* <span data-ttu-id="9b666-497">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="9b666-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="9b666-498">クラスを [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。</span><span class="sxs-lookup"><span data-stu-id="9b666-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="9b666-499">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="9b666-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9b666-500">属性によって有効化される特定の動作については、<xref:web-api/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9b666-501">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="9b666-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9b666-502">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="9b666-503">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="9b666-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="9b666-504">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="9b666-505">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="9b666-506">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="9b666-507">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-507">Add Get methods</span></span>

<span data-ttu-id="9b666-508">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="9b666-509">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="9b666-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="9b666-510">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="9b666-510">Stop the app if it's still running.</span></span> <span data-ttu-id="9b666-511">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="9b666-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="9b666-512">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9b666-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="9b666-513">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9b666-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="9b666-514">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="9b666-515">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="9b666-515">Routing and URL paths</span></span>

<span data-ttu-id="9b666-516">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="9b666-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9b666-517">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9b666-518">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b666-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="9b666-519">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9b666-520">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="9b666-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="9b666-521">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="9b666-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9b666-522">`[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9b666-523">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="9b666-523">This sample doesn't use a template.</span></span> <span data-ttu-id="9b666-524">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9b666-525">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="9b666-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9b666-526">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9b666-527">戻り値</span><span class="sxs-lookup"><span data-stu-id="9b666-527">Return values</span></span>

<span data-ttu-id="9b666-528">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="9b666-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9b666-529">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="9b666-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9b666-530">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9b666-531">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9b666-532">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="9b666-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9b666-533">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9b666-534">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="9b666-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="9b666-535">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="9b666-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9b666-536">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-536">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="9b666-537">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="9b666-538">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="9b666-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9b666-539">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9b666-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9b666-540">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9b666-540">Start the web app.</span></span>
* <span data-ttu-id="9b666-541">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="9b666-541">Start Postman.</span></span>
* <span data-ttu-id="9b666-542">**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b666-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b666-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9b666-544">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9b666-545">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b666-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9b666-546">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="9b666-547">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="9b666-548">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="9b666-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="9b666-549">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-549">Create a new request.</span></span>
  * <span data-ttu-id="9b666-550">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="9b666-551">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="9b666-552">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b666-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="9b666-553">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9b666-554">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-554">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="9b666-556">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-556">Add a Create method</span></span>

<span data-ttu-id="9b666-557">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="9b666-558">上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="9b666-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="9b666-559">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="9b666-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9b666-560">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="9b666-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="9b666-561">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="9b666-562">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="9b666-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9b666-563">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="9b666-564">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="9b666-565">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9b666-566">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9b666-567">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="9b666-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="9b666-568">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="9b666-569">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="9b666-569">Build the project.</span></span>
* <span data-ttu-id="9b666-570">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9b666-571">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="9b666-572">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9b666-573">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9b666-574">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="9b666-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9b666-575">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-575">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="9b666-577">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="9b666-578">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-578">Test the location header URI</span></span>

* <span data-ttu-id="9b666-579">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9b666-580">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="9b666-580">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="9b666-582">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-582">Set the method to GET.</span></span>
* <span data-ttu-id="9b666-583">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="9b666-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="9b666-584">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="9b666-585">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="9b666-586">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="9b666-587">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b666-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9b666-588">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="9b666-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9b666-589">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="9b666-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9b666-590">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b666-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9b666-591">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9b666-592">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="9b666-593">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="9b666-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="9b666-594">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9b666-595">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b666-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9b666-596">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9b666-597">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="9b666-597">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="9b666-599">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="9b666-600">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="9b666-601">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="9b666-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9b666-602">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="9b666-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9b666-603">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b666-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9b666-604">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9b666-605">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="9b666-606">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b666-606">Select **Send**</span></span>

<span data-ttu-id="9b666-607">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="9b666-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="9b666-608">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="9b666-609">jQuery での API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="9b666-609">Call the API with jQuery</span></span>

<span data-ttu-id="9b666-610">このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-610">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="9b666-611">jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。</span><span class="sxs-lookup"><span data-stu-id="9b666-611">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="9b666-612">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-612">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="9b666-613">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b666-613">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="9b666-614">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-614">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="9b666-615">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-615">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="9b666-616">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b666-616">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="9b666-617">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9b666-617">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9b666-618">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-618">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="9b666-619">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="9b666-619">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="9b666-620">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b666-620">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9b666-621">jQuery を取得するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="9b666-621">There are several ways to get jQuery.</span></span> <span data-ttu-id="9b666-622">前のスニペットでは、ライブラリは CDN から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9b666-622">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="9b666-623">このサンプルでは、API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9b666-623">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="9b666-624">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="9b666-624">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="9b666-625">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="9b666-625">Get a list of to-do items</span></span>

<span data-ttu-id="9b666-626">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。</span><span class="sxs-lookup"><span data-stu-id="9b666-626">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="9b666-627">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-627">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="9b666-628">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-628">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="9b666-629">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="9b666-629">Add a to-do item</span></span>

<span data-ttu-id="9b666-630">[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="9b666-630">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="9b666-631">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-631">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="9b666-632">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="9b666-632">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="9b666-633">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="9b666-633">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="9b666-634">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="9b666-634">Update a to-do item</span></span>

<span data-ttu-id="9b666-635">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="9b666-635">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="9b666-636">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="9b666-636">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="9b666-637">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="9b666-637">Delete a to-do item</span></span>

<span data-ttu-id="9b666-638">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="9b666-638">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9b666-639">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9b666-639">Additional resources</span></span>

<span data-ttu-id="9b666-640">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="9b666-640">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="9b666-641">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-641">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="9b666-642">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b666-642">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="9b666-643">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="9b666-643">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
