---
title: ASP.NET Core プロジェクトの Id に対するユーザーデータの追加、ダウンロード、および削除
author: rick-anderson
description: ASP.NET Core プロジェクトの Id にカスタムユーザーデータを追加する方法について説明します。 GDPR ごとのデータを削除します。
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 6daca5776930f80eec8d81132b5a5c4d4d5c13ad
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681163"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="bac16-104">ASP.NET Core プロジェクトで Id にカスタムユーザーデータを追加、ダウンロード、および削除する</span><span class="sxs-lookup"><span data-stu-id="bac16-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="bac16-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bac16-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bac16-106">この記事では、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bac16-106">This article shows how to:</span></span>

* <span data-ttu-id="bac16-107">カスタムユーザーデータを ASP.NET Core web アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="bac16-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="bac16-108">カスタムユーザーデータモデルを <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 属性で装飾して、自動的にダウンロードおよび削除できるようにします。</span><span class="sxs-lookup"><span data-stu-id="bac16-108">Decorate the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="bac16-109">データをダウンロードして削除できるようにすると、 [GDPR](xref:security/gdpr)の要件を満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="bac16-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="bac16-110">プロジェクトサンプルは Razor Pages web アプリから作成されますが、この手順は ASP.NET Core MVC web アプリの場合と似ています。</span><span class="sxs-lookup"><span data-stu-id="bac16-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="bac16-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="bac16-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bac16-112">必要条件</span><span class="sxs-lookup"><span data-stu-id="bac16-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="bac16-113">Razor Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="bac16-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bac16-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bac16-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="bac16-115">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="bac16-116">[ダウンロードするサンプル](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="bac16-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="bac16-117">**ASP.NET Core Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="bac16-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="bac16-118">ドロップダウンで**ASP.NET Core 3.0**を選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="bac16-119">**Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="bac16-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="bac16-120">プロジェクトをビルドおよび実行します。</span><span class="sxs-lookup"><span data-stu-id="bac16-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="bac16-121">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="bac16-122">[ダウンロードするサンプル](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="bac16-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="bac16-123">**ASP.NET Core Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="bac16-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="bac16-124">ドロップダウンで**ASP.NET Core 2.2**を選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="bac16-125">**Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="bac16-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="bac16-126">プロジェクトをビルドおよび実行します。</span><span class="sxs-lookup"><span data-stu-id="bac16-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bac16-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bac16-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="bac16-128">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="bac16-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bac16-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bac16-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bac16-130">**ソリューションエクスプローラー**で、プロジェクトを右クリックして > **新しいスキャフォールディング項目**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="bac16-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="bac16-131">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ > **Identity** ] を選択して **[add]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="bac16-132">**[Id の追加]** ダイアログボックスで、次のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="bac16-133">既存のレイアウト _Layout ファイルを選択し*ます。*</span><span class="sxs-lookup"><span data-stu-id="bac16-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="bac16-134">上書きする以下のファイルを選択してください:</span><span class="sxs-lookup"><span data-stu-id="bac16-134">Select the following files to override:</span></span>
    * <span data-ttu-id="bac16-135">**アカウント/登録**</span><span class="sxs-lookup"><span data-stu-id="bac16-135">**Account/Register**</span></span>
    * <span data-ttu-id="bac16-136">**アカウント/管理/インデックス**</span><span class="sxs-lookup"><span data-stu-id="bac16-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="bac16-137">[ **+** ] ボタンを選択して、新しい**データコンテキストクラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="bac16-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="bac16-138">型 (プロジェクトの名前が**WebApp1**の場合は**WebApp1Context** ) をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="bac16-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="bac16-139">[ **+** ] ボタンを選択して、新しい**ユーザークラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="bac16-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="bac16-140">型を受け入れます (プロジェクトの名前が**WebApp1**の場合は**WebApp1User** ) >**追加** を使用します。</span><span class="sxs-lookup"><span data-stu-id="bac16-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="bac16-141">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bac16-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bac16-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bac16-143">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="bac16-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="bac16-144">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (.csproj) ファイルに追加してください。</span><span class="sxs-lookup"><span data-stu-id="bac16-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="bac16-145">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bac16-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="bac16-146">次のコマンドを実行して、Identity scaffolder オプションの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="bac16-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="bac16-147">プロジェクトフォルダーで、Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="bac16-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="bac16-148">[移行、UseAuthentication、および layout](xref:security/authentication/scaffold-identity#efm)の指示に従って、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="bac16-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="bac16-149">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="bac16-150">`UseAuthentication` に `Startup.Configure` を追加します。</span><span class="sxs-lookup"><span data-stu-id="bac16-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="bac16-151">レイアウトファイルに `<partial name="_LoginPartial" />` を追加します。</span><span class="sxs-lookup"><span data-stu-id="bac16-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="bac16-152">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="bac16-152">Test the app:</span></span>
  * <span data-ttu-id="bac16-153">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="bac16-153">Register a user</span></span>
  * <span data-ttu-id="bac16-154">**[ログアウト]** リンクの横にある新しいユーザー名を選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="bac16-155">場合によっては、ウィンドウを展開するか、ナビゲーションバーのアイコンを選択して、ユーザー名とその他のリンクを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bac16-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="bac16-156">**[Personal Data]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="bac16-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="bac16-157">[Download] \ (**ダウンロード**\) ボタンを選択して、"お持ちの*データ. json*ファイル" を確認します。</span><span class="sxs-lookup"><span data-stu-id="bac16-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="bac16-158">**[削除]** ボタンをテストします。これにより、ログオンしているユーザーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="bac16-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="bac16-159">Id DB にカスタムユーザーデータを追加する</span><span class="sxs-lookup"><span data-stu-id="bac16-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="bac16-160">カスタムプロパティを使用して `IdentityUser` 派生クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="bac16-161">プロジェクトに WebApp1 という名前を付けた場合、ファイルの名前は*Areas/Identity/Data/WebApp1User*になります。</span><span class="sxs-lookup"><span data-stu-id="bac16-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="bac16-162">次のコードを使用して、ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="bac16-163">次[のよう](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)なプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="bac16-163">Properties decorated with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="bac16-164">*区分/id/ページ/アカウント/管理/削除データ*の `UserManager.Delete`を呼び出すと削除されます。</span><span class="sxs-lookup"><span data-stu-id="bac16-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="bac16-165">ダウンロードされたデータには、[*区分/id/ページ]/[アカウント*]/[管理]/[データの追加]/[データの管理/ダウンロード] ページがあります。</span><span class="sxs-lookup"><span data-stu-id="bac16-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="bac16-166">Account/Manage/Index. cshtml ページを更新する</span><span class="sxs-lookup"><span data-stu-id="bac16-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="bac16-167">次の強調表示されたコードを使用して、*区分/id/Pages/Account/Manage/Index. cshtml*の `InputModel` を更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="bac16-168">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/管理/インデックス*を更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="bac16-169">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/管理/インデックス*を更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="bac16-170">Account/Register. cshtml ページを更新する</span><span class="sxs-lookup"><span data-stu-id="bac16-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="bac16-171">次の強調表示されたコードを使用して、[*区分/id/ページ/アカウント/登録*] の `InputModel` を更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="bac16-172">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="bac16-173">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="bac16-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="bac16-174">プロジェクトをビルドする。</span><span class="sxs-lookup"><span data-stu-id="bac16-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="bac16-175">カスタムユーザーデータの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="bac16-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bac16-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bac16-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bac16-177">Visual Studio**パッケージマネージャーコンソール**で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="bac16-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bac16-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bac16-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="bac16-179">テストの作成、表示、ダウンロード、カスタムユーザーデータの削除</span><span class="sxs-lookup"><span data-stu-id="bac16-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="bac16-180">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="bac16-180">Test the app:</span></span>

* <span data-ttu-id="bac16-181">新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="bac16-181">Register a new user.</span></span>
* <span data-ttu-id="bac16-182">[`/Identity/Account/Manage`] ページでカスタムユーザーデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="bac16-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="bac16-183">`/Identity/Account/Manage/PersonalData` ページからユーザーの個人データをダウンロードして表示します。</span><span class="sxs-lookup"><span data-stu-id="bac16-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
