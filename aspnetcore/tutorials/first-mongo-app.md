---
title: ASP.NET Core と MongoDB で Web API を作成する
author: prkhandelwal
description: このチュートリアルは、MongoDB NoSQL データベースを使用して ASP.NET Core Web API を作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 42c0efcd914eaa54134827cdf3bd6bd599d512b2
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2019
ms.locfileid: "73427018"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="0f3b6-103">ASP.NET Core と MongoDB で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="0f3b6-104">作成者: [Pratik Khandelwal](https://twitter.com/K2Prk) および [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0f3b6-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f3b6-105">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="0f3b6-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0f3b6-107">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-107">Configure MongoDB</span></span>
> * <span data-ttu-id="0f3b6-108">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="0f3b6-109">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="0f3b6-110">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="0f3b6-111">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="0f3b6-111">Customize JSON serialization</span></span>

<span data-ttu-id="0f3b6-112">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0f3b6-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0f3b6-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0f3b6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f3b6-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="0f3b6-115">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="0f3b6-115">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="0f3b6-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="0f3b6-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="0f3b6-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0f3b6-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0f3b6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0f3b6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="0f3b6-119">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="0f3b6-119">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0f3b6-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0f3b6-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="0f3b6-121">Visual Studio Code 用 C#</span><span class="sxs-lookup"><span data-stu-id="0f3b6-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="0f3b6-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0f3b6-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0f3b6-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0f3b6-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0f3b6-124">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="0f3b6-124">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0f3b6-125">Visual Studio for Mac バージョン 7.7 以降</span><span class="sxs-lookup"><span data-stu-id="0f3b6-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="0f3b6-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0f3b6-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="0f3b6-127">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-127">Configure MongoDB</span></span>

<span data-ttu-id="0f3b6-128">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="0f3b6-129">*C:\\Program Files\\MongoDB\\Server\\\<バージョン番号>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="0f3b6-130">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="0f3b6-131">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="0f3b6-132">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="0f3b6-133">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="0f3b6-134">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="0f3b6-135">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="0f3b6-136">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="0f3b6-137">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-137">Open a command shell.</span></span> <span data-ttu-id="0f3b6-138">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="0f3b6-139">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="0f3b6-140">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-140">Open another command shell instance.</span></span> <span data-ttu-id="0f3b6-141">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="0f3b6-142">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="0f3b6-143">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="0f3b6-144">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="0f3b6-145">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="0f3b6-146">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="0f3b6-147">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="0f3b6-148">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="0f3b6-149">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="0f3b6-150">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="0f3b6-151">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="0f3b6-152">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="0f3b6-153">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-153">The database is ready.</span></span> <span data-ttu-id="0f3b6-154">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="0f3b6-155">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0f3b6-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f3b6-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0f3b6-157">**[ファイル]** > **[新規]** > **[プロジェクト]** の順に移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="0f3b6-158">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="0f3b6-159">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="0f3b6-160">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="0f3b6-161">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="0f3b6-162">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0f3b6-163">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="0f3b6-164">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0f3b6-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0f3b6-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0f3b6-166">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="0f3b6-167">.NET Core をターゲットとする新しい ASP.NET Core Web API プロジェクトが生成され、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="0f3b6-168">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'BooksApi' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="0f3b6-169">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-169">Select **Yes**.</span></span>
1. <span data-ttu-id="0f3b6-170">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0f3b6-171">**[統合端末]** を開き、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="0f3b6-172">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0f3b6-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0f3b6-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0f3b6-174">**[ファイル]** > **[新しいソリューション]** > **[.NET Core]** > **[アプリ]** の順に移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="0f3b6-175">**[ASP.NET Core Web API]** C# プロジェクト テンプレートを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="0f3b6-176">**[ターゲット フレームワーク]** ドロップダウン リストで **[.NET Core 3.0]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="0f3b6-177">**[プロジェクト名]** に「*BooksApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="0f3b6-178">**[ソリューション]** パッドで、プロジェクトの **[依存関係]** ノードを右クリックし、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="0f3b6-179">検索ボックスに「*MongoDB.Driver*」と入力し、*MongoDB.Driver* パッケージを選択して、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="0f3b6-180">**[ライセンスへの同意]** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="0f3b6-181">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-181">Add an entity model</span></span>

1. <span data-ttu-id="0f3b6-182">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="0f3b6-183">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="0f3b6-184">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="0f3b6-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="0f3b6-185">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="0f3b6-186">ドキュメントの主キーとしてこのプロパティを指定するために、[[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-186">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="0f3b6-187">パラメーターを [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく型 `string` として渡すことができるように、[[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-187">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="0f3b6-188">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="0f3b6-189">`BookName` プロパティには、[[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-189">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="0f3b6-190">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="0f3b6-191">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-191">Add a configuration model</span></span>

1. <span data-ttu-id="0f3b6-192">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="0f3b6-193">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="0f3b6-194">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="0f3b6-195">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="0f3b6-196">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="0f3b6-197">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-197">In the preceding code:</span></span>

   * <span data-ttu-id="0f3b6-198">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="0f3b6-199">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="0f3b6-200">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0f3b6-201">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="0f3b6-202">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="0f3b6-203">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="0f3b6-204">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="0f3b6-205">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="0f3b6-206">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="0f3b6-207">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="0f3b6-208">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="0f3b6-209">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="0f3b6-210">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="0f3b6-211">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="0f3b6-212">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="0f3b6-213">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="0f3b6-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="0f3b6-215">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="0f3b6-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="0f3b6-217">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使って、特定のコレクション内のデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="0f3b6-218">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="0f3b6-219">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="0f3b6-220">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="0f3b6-221">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="0f3b6-222">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="0f3b6-223">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="0f3b6-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="0f3b6-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="0f3b6-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="0f3b6-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0f3b6-228">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="0f3b6-228">Add a controller</span></span>

<span data-ttu-id="0f3b6-229">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="0f3b6-230">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="0f3b6-230">The preceding web API controller:</span></span>

* <span data-ttu-id="0f3b6-231">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="0f3b6-232">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="0f3b6-233">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="0f3b6-234">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="0f3b6-235">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="0f3b6-236">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="0f3b6-237">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="0f3b6-237">Test the web API</span></span>

1. <span data-ttu-id="0f3b6-238">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-238">Build and run the app.</span></span>

1. <span data-ttu-id="0f3b6-239">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="0f3b6-240">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-240">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="0f3b6-241">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="0f3b6-242">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="0f3b6-243">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-243">Configure JSON serialization options</span></span>

<span data-ttu-id="0f3b6-244">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="0f3b6-245">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="0f3b6-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="0f3b6-246">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="0f3b6-247">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="0f3b6-248">JSON.NET は ASP.NET 共有フレームワークから削除されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="0f3b6-249">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="0f3b6-250">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddMvc` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="0f3b6-251">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="0f3b6-252">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="0f3b6-253">*Models/Book.cs* では、`BookName` プロパティに次の [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-253">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="0f3b6-254">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="0f3b6-255">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="0f3b6-256">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="0f3b6-257">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0f3b6-258">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="0f3b6-259">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0f3b6-260">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-260">Configure MongoDB</span></span>
> * <span data-ttu-id="0f3b6-261">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="0f3b6-262">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="0f3b6-263">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="0f3b6-264">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="0f3b6-264">Customize JSON serialization</span></span>

<span data-ttu-id="0f3b6-265">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-265">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0f3b6-266">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0f3b6-266">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0f3b6-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f3b6-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="0f3b6-268">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0f3b6-268">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="0f3b6-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="0f3b6-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="0f3b6-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0f3b6-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0f3b6-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0f3b6-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="0f3b6-272">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0f3b6-272">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0f3b6-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0f3b6-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="0f3b6-274">Visual Studio Code 用 C#</span><span class="sxs-lookup"><span data-stu-id="0f3b6-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="0f3b6-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0f3b6-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0f3b6-276">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0f3b6-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0f3b6-277">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0f3b6-277">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0f3b6-278">Visual Studio for Mac バージョン 7.7 以降</span><span class="sxs-lookup"><span data-stu-id="0f3b6-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="0f3b6-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0f3b6-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="0f3b6-280">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-280">Configure MongoDB</span></span>

<span data-ttu-id="0f3b6-281">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="0f3b6-282">*C:\\Program Files\\MongoDB\\Server\\\<バージョン番号>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="0f3b6-283">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="0f3b6-284">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="0f3b6-285">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="0f3b6-286">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="0f3b6-287">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="0f3b6-288">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="0f3b6-289">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="0f3b6-290">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-290">Open a command shell.</span></span> <span data-ttu-id="0f3b6-291">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="0f3b6-292">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="0f3b6-293">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-293">Open another command shell instance.</span></span> <span data-ttu-id="0f3b6-294">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="0f3b6-295">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="0f3b6-296">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="0f3b6-297">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="0f3b6-298">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="0f3b6-299">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="0f3b6-300">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="0f3b6-301">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-301">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="0f3b6-302">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="0f3b6-303">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="0f3b6-304">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-304">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="0f3b6-305">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="0f3b6-306">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-306">The database is ready.</span></span> <span data-ttu-id="0f3b6-307">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="0f3b6-308">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0f3b6-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f3b6-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0f3b6-310">**[ファイル]** > **[新規]** > **[プロジェクト]** の順に移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="0f3b6-311">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="0f3b6-312">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="0f3b6-313">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 2.2]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="0f3b6-314">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="0f3b6-315">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0f3b6-316">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="0f3b6-317">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0f3b6-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0f3b6-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0f3b6-319">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="0f3b6-320">.NET Core をターゲットとする新しい ASP.NET Core Web API プロジェクトが生成され、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="0f3b6-321">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'BooksApi' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="0f3b6-322">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-322">Select **Yes**.</span></span>
1. <span data-ttu-id="0f3b6-323">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0f3b6-324">**[統合端末]** を開き、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="0f3b6-325">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0f3b6-326">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0f3b6-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0f3b6-327">**[ファイル]** > **[新しいソリューション]** > **[.NET Core]** > **[アプリ]** の順に移動します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="0f3b6-328">**[ASP.NET Core Web API]** C# プロジェクト テンプレートを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="0f3b6-329">**[ターゲット フレームワーク]** ドロップダウン リストで **[.NET Core 2.2]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="0f3b6-330">**[プロジェクト名]** に「*BooksApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="0f3b6-331">**[ソリューション]** パッドで、プロジェクトの **[依存関係]** ノードを右クリックし、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="0f3b6-332">検索ボックスに「*MongoDB.Driver*」と入力し、*MongoDB.Driver* パッケージを選択して、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="0f3b6-333">**[ライセンスへの同意]** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="0f3b6-334">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-334">Add an entity model</span></span>

1. <span data-ttu-id="0f3b6-335">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="0f3b6-336">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="0f3b6-337">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="0f3b6-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="0f3b6-338">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="0f3b6-339">ドキュメントの主キーとしてこのプロパティを指定するために、[[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-339">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="0f3b6-340">パラメーターを [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく型 `string` として渡すことができるように、[[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-340">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="0f3b6-341">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="0f3b6-342">`BookName` プロパティには、[[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-342">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="0f3b6-343">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="0f3b6-344">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-344">Add a configuration model</span></span>

1. <span data-ttu-id="0f3b6-345">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="0f3b6-346">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="0f3b6-347">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="0f3b6-348">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="0f3b6-349">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="0f3b6-350">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-350">In the preceding code:</span></span>

   * <span data-ttu-id="0f3b6-351">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="0f3b6-352">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="0f3b6-353">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0f3b6-354">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="0f3b6-355">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="0f3b6-356">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="0f3b6-357">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="0f3b6-358">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="0f3b6-359">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="0f3b6-360">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="0f3b6-361">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="0f3b6-362">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="0f3b6-363">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="0f3b6-364">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="0f3b6-365">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="0f3b6-366">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="0f3b6-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="0f3b6-368">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="0f3b6-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="0f3b6-370">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使って、特定のコレクション内のデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="0f3b6-371">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="0f3b6-372">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="0f3b6-373">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="0f3b6-374">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="0f3b6-375">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="0f3b6-376">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="0f3b6-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="0f3b6-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="0f3b6-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="0f3b6-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0f3b6-381">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="0f3b6-381">Add a controller</span></span>

<span data-ttu-id="0f3b6-382">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="0f3b6-383">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="0f3b6-383">The preceding web API controller:</span></span>

* <span data-ttu-id="0f3b6-384">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="0f3b6-385">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="0f3b6-386">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="0f3b6-387">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="0f3b6-388">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="0f3b6-389">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="0f3b6-390">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="0f3b6-390">Test the web API</span></span>

1. <span data-ttu-id="0f3b6-391">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-391">Build and run the app.</span></span>

1. <span data-ttu-id="0f3b6-392">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="0f3b6-393">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-393">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="0f3b6-394">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="0f3b6-395">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="0f3b6-396">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="0f3b6-396">Configure JSON serialization options</span></span>

<span data-ttu-id="0f3b6-397">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="0f3b6-398">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="0f3b6-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="0f3b6-399">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="0f3b6-400">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="0f3b6-401">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddMvc` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="0f3b6-402">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="0f3b6-403">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="0f3b6-404">*Models/Book.cs* では、`BookName` プロパティに次の [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-404">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="0f3b6-405">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="0f3b6-406">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="0f3b6-407">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="0f3b6-408">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="0f3b6-409">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="0f3b6-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="0f3b6-410">次の手順</span><span class="sxs-lookup"><span data-stu-id="0f3b6-410">Next steps</span></span>

<span data-ttu-id="0f3b6-411">ASP.NET Core Web API の構築について詳しくは、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0f3b6-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="0f3b6-412">この記事の YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="0f3b6-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
