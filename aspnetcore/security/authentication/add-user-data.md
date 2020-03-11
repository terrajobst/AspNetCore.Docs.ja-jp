---
title: ASP.NET Core プロジェクトにおける Identity へのカスタムユーザーデータの追加、ダウンロードおよび削除
author: rick-anderson
description: ASP.NET Core プロジェクトにおいて Identity にカスタム ユーザーデータを追加する方法について説明します。 GDPR ごとのデータを削除します。
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 7a67f55da0e685ed3fd5badb30e8be683411a5ae
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653282"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="60ec5-104">ASP.NET Core プロジェクトにおける Identity へのカスタム ユーザーデータの追加、ダウンロードおよび削除</span><span class="sxs-lookup"><span data-stu-id="60ec5-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="60ec5-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="60ec5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="60ec5-106">この記事では、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-106">This article shows how to:</span></span>

* <span data-ttu-id="60ec5-107">ASP.NET Core web アプリにカスタム ユーザー データを追加します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="60ec5-108">カスタムユーザーデータモデルを <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 属性でマークして、自動的にダウンロードおよび削除できるようにします。</span><span class="sxs-lookup"><span data-stu-id="60ec5-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="60ec5-109">データをダウンロードして削除できるようにすると、 [GDPR](xref:security/gdpr)の要件を満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="60ec5-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="60ec5-110">プロジェクト サンプルは、Razor ページ web アプリから作成されますが、手順は ASP.NET Core MVC web アプリと同様。</span><span class="sxs-lookup"><span data-stu-id="60ec5-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="60ec5-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="60ec5-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60ec5-112">前提条件</span><span class="sxs-lookup"><span data-stu-id="60ec5-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="60ec5-113">Razor Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="60ec5-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60ec5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60ec5-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="60ec5-115">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、 > [プロジェクト]\*\* の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="60ec5-116">[ダウンロードするサンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="60ec5-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="60ec5-117">**ASP.NET Core Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="60ec5-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="60ec5-118">ドロップダウンで**ASP.NET Core 3.0**を選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="60ec5-119">**Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="60ec5-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="60ec5-120">プロジェクトをビルドおよび実行します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="60ec5-121">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、 > [プロジェクト]\*\* の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="60ec5-122">[ダウンロードするサンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="60ec5-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="60ec5-123">**ASP.NET Core Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="60ec5-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="60ec5-124">ドロップダウンで**ASP.NET Core 2.2**を選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="60ec5-125">**Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="60ec5-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="60ec5-126">プロジェクトをビルドおよび実行します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="60ec5-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="60ec5-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="60ec5-128">Identity のスキャフォールディングの実行</span><span class="sxs-lookup"><span data-stu-id="60ec5-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60ec5-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60ec5-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60ec5-130">**ソリューションエクスプローラー**で、プロジェクトを右クリックして > **新しいスキャフォールディング項目**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="60ec5-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="60ec5-131">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ > **Identity** ] を選択して **[add]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="60ec5-132">**[Id の追加]** ダイアログボックスで、次のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="60ec5-133">既存のレイアウト _Layout ファイルを選択し*ます。*</span><span class="sxs-lookup"><span data-stu-id="60ec5-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="60ec5-134">オーバーライドする次のファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-134">Select the following files to override:</span></span>
    * <span data-ttu-id="60ec5-135">**アカウント/登録**</span><span class="sxs-lookup"><span data-stu-id="60ec5-135">**Account/Register**</span></span>
    * <span data-ttu-id="60ec5-136">**アカウント/管理/インデックス**</span><span class="sxs-lookup"><span data-stu-id="60ec5-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="60ec5-137">[ **+** ] ボタンを選択して、新しい**データコンテキストクラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="60ec5-138">型 (プロジェクトの名前が**WebApp1**の場合は**WebApp1Context** ) をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="60ec5-139">[ **+** ] ボタンを選択して、新しい**ユーザークラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="60ec5-140">型を受け入れます (プロジェクトの名前が**WebApp1**の場合は**WebApp1User** ) >**追加** を使用します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="60ec5-141">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="60ec5-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="60ec5-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="60ec5-143">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="60ec5-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="60ec5-144">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (.csproj) ファイルに追加してください。</span><span class="sxs-lookup"><span data-stu-id="60ec5-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="60ec5-145">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="60ec5-146">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="60ec5-147">プロジェクト フォルダーでは、Identity scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="60ec5-148">[移行、UseAuthentication、および layout](xref:security/authentication/scaffold-identity#efm)の指示に従って、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="60ec5-149">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="60ec5-150">`UseAuthentication` を `Startup.Configure` に追加します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="60ec5-151">レイアウトファイルに `<partial name="_LoginPartial" />` を追加します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="60ec5-152">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="60ec5-152">Test the app:</span></span>
  * <span data-ttu-id="60ec5-153">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="60ec5-153">Register a user</span></span>
  * <span data-ttu-id="60ec5-154">**[ログアウト]** リンクの横にある新しいユーザー名を選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="60ec5-155">ウィンドウを拡大またはユーザー名とその他のリンクを表示するナビゲーション バーのアイコンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="60ec5-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="60ec5-156">**[Personal Data]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="60ec5-157">[Download] \ (**ダウンロード**\) ボタンを選択して、"お持ちの*データ. json*ファイル" を確認します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="60ec5-158">**[削除]** ボタンをテストします。これにより、ログオンしているユーザーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="60ec5-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="60ec5-159">Identity DB へのカスタム ユーザーデータの追加</span><span class="sxs-lookup"><span data-stu-id="60ec5-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="60ec5-160">カスタムプロパティを使用して `IdentityUser` 派生クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="60ec5-161">プロジェクトに WebApp1 という名前を付けた場合、ファイルの名前は*Areas/Identity/Data/WebApp1User*になります。</span><span class="sxs-lookup"><span data-stu-id="60ec5-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="60ec5-162">次のコード ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="60ec5-163">プロパティ[は、次のよう](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)になります。</span><span class="sxs-lookup"><span data-stu-id="60ec5-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="60ec5-164">*区分/id/ページ/アカウント/管理/削除データ*の `UserManager.Delete`を呼び出すと削除されます。</span><span class="sxs-lookup"><span data-stu-id="60ec5-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="60ec5-165">ダウンロードされたデータには、[*区分/id/ページ]/[アカウント*]/[管理]/[データの追加]/[データの管理/ダウンロード] ページがあります。</span><span class="sxs-lookup"><span data-stu-id="60ec5-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="60ec5-166">Account/Manage/Index.cshtml ページの更新</span><span class="sxs-lookup"><span data-stu-id="60ec5-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="60ec5-167">次の強調表示されたコードを使用して、*区分/id/Pages/Account/Manage/Index. cshtml*の `InputModel` を更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="60ec5-168">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/管理/インデックス*を更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="60ec5-169">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/管理/インデックス*を更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="60ec5-170">Account/Register.cshtml ページの更新</span><span class="sxs-lookup"><span data-stu-id="60ec5-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="60ec5-171">次の強調表示されたコードを使用して、[*区分/id/ページ/アカウント/登録*] の `InputModel` を更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="60ec5-172">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="60ec5-173">次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="60ec5-174">プロジェクトをビルドする。</span><span class="sxs-lookup"><span data-stu-id="60ec5-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="60ec5-175">カスタム ユーザー データのマイグレーションの追加</span><span class="sxs-lookup"><span data-stu-id="60ec5-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60ec5-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60ec5-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60ec5-177">Visual Studio**パッケージマネージャーコンソール**で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="60ec5-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="60ec5-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="60ec5-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="60ec5-179">カスタム ユーザー データの作成、表示、ダウンロード、削除のテスト</span><span class="sxs-lookup"><span data-stu-id="60ec5-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="60ec5-180">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="60ec5-180">Test the app:</span></span>

* <span data-ttu-id="60ec5-181">新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-181">Register a new user.</span></span>
* <span data-ttu-id="60ec5-182">[`/Identity/Account/Manage`] ページでカスタムユーザーデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="60ec5-183">`/Identity/Account/Manage/PersonalData` ページからユーザーの個人データをダウンロードして表示します。</span><span class="sxs-lookup"><span data-stu-id="60ec5-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
