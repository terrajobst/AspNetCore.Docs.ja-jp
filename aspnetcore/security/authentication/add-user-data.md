---
title: ASP.NETコア プロジェクトの Identity にユーザー データを追加、ダウンロード、および削除する
author: rick-anderson
description: ASP.NET Core プロジェクトでカスタム ユーザー データを Identity に追加する方法について説明します。 GDPR ごとにデータを削除します。
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501230"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="cb939-104">ASP.NETコア プロジェクトの Identity にカスタム ユーザー データを追加、ダウンロード、および削除する</span><span class="sxs-lookup"><span data-stu-id="cb939-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="cb939-105">著者 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb939-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb939-106">この記事では、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cb939-106">This article shows how to:</span></span>

* <span data-ttu-id="cb939-107">ASP.NETコア Web アプリにカスタム ユーザー データを追加します。</span><span class="sxs-lookup"><span data-stu-id="cb939-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="cb939-108">カスタム ユーザー データ モデルに<xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>属性をマークし、自動的にダウンロードおよび削除できるようにします。</span><span class="sxs-lookup"><span data-stu-id="cb939-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="cb939-109">データをダウンロードして削除できるようにすることで[、GDPR](xref:security/gdpr)要件を満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="cb939-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="cb939-110">プロジェクト サンプルは Razor ページ Web アプリから作成されますが、ASP.NET Core MVC Web アプリの手順は似ています。</span><span class="sxs-lookup"><span data-stu-id="cb939-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="cb939-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cb939-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cb939-112">前提条件</span><span class="sxs-lookup"><span data-stu-id="cb939-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="cb939-113">Razor Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="cb939-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb939-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb939-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="cb939-115">Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="cb939-116">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致する場合は、プロジェクト**WebApp1**に名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="cb939-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="cb939-117">**コア Web アプリケーション**>ASP.NET**選択OK**</span><span class="sxs-lookup"><span data-stu-id="cb939-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="cb939-118">ドロップダウン**でASP.NET Core 3.0**を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="cb939-119">**Web アプリケーションの**>選択**OK**</span><span class="sxs-lookup"><span data-stu-id="cb939-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="cb939-120">プロジェクトをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="cb939-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="cb939-121">Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="cb939-122">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致する場合は、プロジェクト**WebApp1**に名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="cb939-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="cb939-123">**コア Web アプリケーション**>ASP.NET**選択OK**</span><span class="sxs-lookup"><span data-stu-id="cb939-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="cb939-124">ドロップダウン**ASP.NET Core 2.2**を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="cb939-125">**Web アプリケーションの**>選択**OK**</span><span class="sxs-lookup"><span data-stu-id="cb939-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="cb939-126">プロジェクトをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="cb939-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="cb939-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb939-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="cb939-128">ID スキャフフォルダーを実行する</span><span class="sxs-lookup"><span data-stu-id="cb939-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb939-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb939-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb939-130">**ソリューション エクスプローラ**で、プロジェクトを右クリック>**新しいスキャフォールディング項目の\*\*\*\*追加** > ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cb939-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="cb939-131">[**スキャフォールディングの追加**] ダイアログの左ペインで、[**識別情報** > **の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="cb939-132">[ID の**追加**] ダイアログで、次のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="cb939-133">既存のレイアウト ファイル*を選択する ~/ページ/共有/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cb939-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="cb939-134">上書きする次のファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-134">Select the following files to override:</span></span>
    * <span data-ttu-id="cb939-135">**アカウント/登録**</span><span class="sxs-lookup"><span data-stu-id="cb939-135">**Account/Register**</span></span>
    * <span data-ttu-id="cb939-136">**アカウント/管理/インデックス**</span><span class="sxs-lookup"><span data-stu-id="cb939-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="cb939-137">ボタンを**+** 選択して、新しい**Data コンテキスト クラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="cb939-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="cb939-138">プロジェクトが WebApp1 という名前の場合は、型を受け入れます (**WebApp1.Models.WebApp1Context** )。 **WebApp1**</span><span class="sxs-lookup"><span data-stu-id="cb939-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="cb939-139">ボタンを**+** 選択して新しい**User クラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="cb939-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="cb939-140">プロジェクト名が**WebApp1**の場合は、タイプ ( **WebApp1User**) を受け入>**追加**します。</span><span class="sxs-lookup"><span data-stu-id="cb939-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="cb939-141">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb939-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb939-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb939-143">ASP.NET Core scaffolder を以前にインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="cb939-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="cb939-144">プロジェクト (.csproj)[ファイルにパッケージ](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="cb939-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="cb939-145">プロジェクト ディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cb939-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="cb939-146">次のコマンドを実行して、ID スキャフフォルダーオプションを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="cb939-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="cb939-147">プロジェクト フォルダーで、ID スキャフフォルダーを実行します。</span><span class="sxs-lookup"><span data-stu-id="cb939-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="cb939-148">[移行、UseAuthentication、およびレイアウト](xref:security/authentication/scaffold-identity#efm)の指示に従って、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="cb939-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="cb939-149">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="cb939-150">`UseAuthentication` を `Startup.Configure` に追加します。</span><span class="sxs-lookup"><span data-stu-id="cb939-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="cb939-151">レイアウト`<partial name="_LoginPartial" />`ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="cb939-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="cb939-152">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="cb939-152">Test the app:</span></span>
  * <span data-ttu-id="cb939-153">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="cb939-153">Register a user</span></span>
  * <span data-ttu-id="cb939-154">新しいユーザー名を選択します (**ログアウト**リンクの横)。</span><span class="sxs-lookup"><span data-stu-id="cb939-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="cb939-155">ユーザー名やその他のリンクを表示するには、ウィンドウを展開するか、ナビゲーション バーのアイコンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cb939-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="cb939-156">[**個人データ**] タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="cb939-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="cb939-157">[**ダウンロード**] ボタンを選択し、*パーソナルデータ.json*ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="cb939-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="cb939-158">[**削除**] ボタンをテストします。</span><span class="sxs-lookup"><span data-stu-id="cb939-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="cb939-159">カスタム ユーザー データを ID DB に追加する</span><span class="sxs-lookup"><span data-stu-id="cb939-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="cb939-160">派生クラス`IdentityUser`をカスタム プロパティで更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="cb939-161">プロジェクト WebApp1 という名前のファイルは、*エリア/ID/データ/WebApp1User.cs*という名前になります。</span><span class="sxs-lookup"><span data-stu-id="cb939-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="cb939-162">次のコードでファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="cb939-163">[パーソナルデータ](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)属性を持つプロパティは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cb939-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="cb939-164">*エリア/アイデンティティ/ページ/アカウント/管理/削除パーソナルデータ.cshtml*カミソリページが呼び`UserManager.Delete`出されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="cb939-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="cb939-165">*エリア/アイデンティティ/ページ/アカウント/管理/ダウンロードパーソナルデータ.cshtml*カミソリページによってダウンロードされたデータに含まれています。</span><span class="sxs-lookup"><span data-stu-id="cb939-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="cb939-166">アカウント/管理/インデックス.cshtml ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="cb939-167">次の`InputModel`強調表示されたコードを使用して *、領域/ID/ページ/アカウント/管理/Index.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="cb939-168">次の強調表示されたマークアップで*領域/ID/ページ/アカウント/管理/Index.cshtml*を更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="cb939-169">次の強調表示されたマークアップで*領域/ID/ページ/アカウント/管理/Index.cshtml*を更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="cb939-170">アカウント/レジスタ.cshtmlページを更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="cb939-171">次の`InputModel`強調表示されたコードを使用して *、領域/ID/ページ/アカウント/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="cb939-172">次の強調表示されたマークアップで*領域/ID/ページ/アカウント/Register.cshtml*を更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="cb939-173">次の強調表示されたマークアップで*領域/ID/ページ/アカウント/Register.cshtml*を更新します。</span><span class="sxs-lookup"><span data-stu-id="cb939-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="cb939-174">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="cb939-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="cb939-175">カスタム ユーザー データの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="cb939-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb939-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb939-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb939-177">Visual Studio**パッケージ マネージャー コンソールで次の手順を実行します**。</span><span class="sxs-lookup"><span data-stu-id="cb939-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cb939-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cb939-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="cb939-179">カスタム ユーザー データの作成、表示、ダウンロード、削除をテストする</span><span class="sxs-lookup"><span data-stu-id="cb939-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="cb939-180">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="cb939-180">Test the app:</span></span>

* <span data-ttu-id="cb939-181">新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="cb939-181">Register a new user.</span></span>
* <span data-ttu-id="cb939-182">ページ上のカスタム ユーザー`/Identity/Account/Manage`データを表示します。</span><span class="sxs-lookup"><span data-stu-id="cb939-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="cb939-183">ページからユーザーの個人データをダウンロードして`/Identity/Account/Manage/PersonalData`表示します。</span><span class="sxs-lookup"><span data-stu-id="cb939-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="cb939-184">Id にクレームを追加します。<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="cb939-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="cb939-185">インターフェイスを使用して、ASP.NETコア ID にクレーム`IUserClaimsPrincipalFactory<T>`を追加できます。</span><span class="sxs-lookup"><span data-stu-id="cb939-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="cb939-186">このクラスは、メソッド内のアプリに`Startup.ConfigureServices`追加できます。</span><span class="sxs-lookup"><span data-stu-id="cb939-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cb939-187">次のようにクラスのカスタム実装を追加します。</span><span class="sxs-lookup"><span data-stu-id="cb939-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="cb939-188">デモ コードでは、`ApplicationUser`クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="cb939-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="cb939-189">このクラスは、`IsAdmin`追加のクレームを追加するために使用されるプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="cb939-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="cb939-190">`AdditionalUserClaimsPrincipalFactory` は、`UserClaimsPrincipalFactory` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="cb939-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="cb939-191">新しいロール要求が に追加`ClaimsPrincipal`されます。</span><span class="sxs-lookup"><span data-stu-id="cb939-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="cb939-192">その後、追加の要求をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cb939-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="cb939-193">Razor ページでは、インスタンス`IAuthorizationService`を使用して要求値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="cb939-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
